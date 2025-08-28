<template>
  <div class="container mx-auto p-6 max-w-4xl">
    <h1 class="text-3xl font-bold mb-6">
      WebRTC, Passing SDP with no signaling.
    </h1>

    <!-- Video elements -->
    <div class="grid grid-cols-1 md:grid-cols-2 gap-4 mb-6">
      <div class="bg-gray-100 rounded-lg p-4">
        <h3 class="text-lg font-semibold mb-2">Local Video (User 1)</h3>
        <video
          id="user-1"
          ref="localVideo"
          autoplay
          playsinline
          muted
          class="w-full h-64 bg-black rounded"
        ></video>
      </div>

      <div class="bg-gray-100 rounded-lg p-4">
        <h3 class="text-lg font-semibold mb-2">Remote Video (User 2)</h3>
        <video
          id="user-2"
          ref="remoteVideo"
          autoplay
          playsinline
          class="w-full h-64 bg-black rounded"
        ></video>
      </div>
    </div>

    <!-- Controls -->
    <div class="grid grid-cols-1 md:grid-cols-3 gap-4 mb-6">
      <button
        id="create-offer"
        @click="createOffer"
        class="bg-blue-500 hover:bg-blue-700 text-white font-bold py-2 px-4 rounded"
      >
        Create Offer
      </button>

      <button
        id="create-answer"
        @click="createAnswer"
        class="bg-green-500 hover:bg-green-700 text-white font-bold py-2 px-4 rounded"
      >
        Create Answer
      </button>

      <button
        id="add-answer"
        @click="addAnswer"
        class="bg-purple-500 hover:bg-purple-700 text-white font-bold py-2 px-4 rounded"
      >
        Add Answer
      </button>
    </div>

    <!-- SDP Text Areas -->
    <div class="grid grid-cols-1 md:grid-cols-2 gap-4">
      <div class="bg-gray-50 rounded-lg p-4">
        <h3 class="text-lg font-semibold mb-2">Offer SDP</h3>
        <textarea
          id="offer-sdp"
          ref="offerSdp"
          placeholder="Offer SDP will appear here..."
          class="w-full h-32 p-2 border border-gray-300 rounded resize-none text-xs font-mono"
        ></textarea>
      </div>

      <div class="bg-gray-50 rounded-lg p-4">
        <h3 class="text-lg font-semibold mb-2">Answer SDP</h3>
        <textarea
          id="answer-sdp"
          ref="answerSdp"
          placeholder="Answer SDP will appear here..."
          class="w-full h-32 p-2 border border-gray-300 rounded resize-none text-xs font-mono"
        ></textarea>
      </div>
    </div>
  </div>
</template>

<script lang="ts" setup>
const localVideo = ref<HTMLVideoElement>();
const remoteVideo = ref<HTMLVideoElement>();
const offerSdp = ref<HTMLTextAreaElement>();
const answerSdp = ref<HTMLTextAreaElement>();

const peerConnection = ref<RTCPeerConnection>();
const localStream = ref<MediaStream>();
const remoteStream = ref<MediaStream>();

const init = async () => {
  try {
    // Initialize peer connection
    peerConnection.value = new RTCPeerConnection();

    // Get user media
    localStream.value = await navigator.mediaDevices.getUserMedia({
      video: true,
      audio: false,
    });

    // Initialize remote stream
    remoteStream.value = new MediaStream();

    // Set video sources
    if (localVideo.value) {
      if (!localStream.value) return;
      localVideo.value.srcObject = localStream.value;
    }
    if (remoteVideo.value) {
      remoteVideo.value.srcObject = remoteStream.value;
    }

    // Add local tracks to peer connection
    localStream.value.getTracks().forEach((track) => {
      if (!peerConnection.value) return;
      if (!localStream.value) return;
      peerConnection.value.addTrack(track, localStream.value);
    });

    // Handle incoming tracks
    peerConnection.value.ontrack = (event) => {
      if (event.streams && event.streams[0]) {
        event.streams[0].getTracks().forEach((track) => {
          if (!remoteStream.value) return;
          remoteStream.value.addTrack(track);
        });
      }
    };

    console.log("WebRTC initialized successfully");
  } catch (error) {
    console.error("Error initializing WebRTC:", error);
  }
};

const createOffer = async () => {
  try {
    if (!peerConnection.value) return;

    peerConnection.value.onicecandidate = async (event) => {
      // Event that fires off when a new offer ICE candidate is created
      if (event.candidate) {
        if (offerSdp.value) {
          offerSdp.value.value = JSON.stringify(
            peerConnection.value && peerConnection.value.localDescription
          );
        }
      }
    };

    const offer = await peerConnection.value.createOffer();
    await peerConnection.value.setLocalDescription(offer);
    console.log("Offer created successfully");
  } catch (error) {
    console.error("Error creating offer:", error);
  }
};

const createAnswer = async () => {
  try {
    if (!peerConnection.value) return;

    if (!offerSdp.value?.value) {
      alert("Please create an offer first");
      return;
    }

    const offer = JSON.parse(offerSdp.value.value);

    peerConnection.value.onicecandidate = async (event) => {
      // Event that fires off when a new answer ICE candidate is created
      if (event.candidate) {
        console.log("Adding answer candidate...:", event.candidate);
        if (answerSdp.value) {
          answerSdp.value.value = JSON.stringify(
            peerConnection.value && peerConnection.value.localDescription
          );
        }
      }
    };

    await peerConnection.value.setRemoteDescription(offer);

    const answer = await peerConnection.value.createAnswer();
    await peerConnection.value.setLocalDescription(answer);
    console.log("Answer created successfully");
  } catch (error) {
    console.error("Error creating answer:", error);
  }
};

const addAnswer = async () => {
  try {
    if (!peerConnection.value) return;

    console.log("Add answer triggered");

    if (!answerSdp.value?.value) {
      alert("Please create an answer first");
      return;
    }

    const answer = JSON.parse(answerSdp.value.value);
    console.log("answer:", answer);

    if (!peerConnection.value.currentRemoteDescription) {
      await peerConnection.value.setRemoteDescription(answer);
      console.log("Answer added successfully");
    } else {
      console.log("Remote description already set");
    }
  } catch (error) {
    console.error("Error adding answer:", error);
  }
};

// Initialize when component is mounted
onMounted(() => {
  init();
});

// Cleanup when component is unmounted
onUnmounted(() => {
  if (localStream.value) {
    localStream.value.getTracks().forEach((track) => track.stop());
  }
  if (peerConnection.value) {
    peerConnection.value.close();
  }
});
</script>
