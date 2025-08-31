<template>
  <div class="min-h-screen tactical-pattern bg-[var(--background)]">
    <div class="container mx-auto p-6 max-w-7xl space-y-8">
      <!-- Header Section -->
      <Header :connectionBadgeVariant :connectionStatus />

      <!-- Error Alert -->
      <Alert
        v-if="error"
        variant="destructive"
        class="tactical-card border-red-500/50 bg-red-500/5 transition-all duration-300"
      >
        <AlertCircle class="h-4 w-4" />
        <AlertTitle class="font-bold tracking-wide text-red-600"
          >SYSTEM ALERT</AlertTitle
        >
        <AlertDescription class="flex items-center justify-between">
          <span class="font-medium text-red-700">{{ error }}</span>
          <Button
            variant="ghost"
            size="sm"
            @click="dismissError"
            class="text-red-600 hover:text-red-500 hover:bg-red-500/10"
          >
            <X class="h-4 w-4" />
          </Button>
        </AlertDescription>
      </Alert>

      <!-- Video Section -->
      <VideoRTC :localStream :remoteStream />

      <!-- Controls Section -->
      <Controls
        @addAnswer="addAnswer"
        @createAnswer="createAnswer"
        @createOffer="createOffer"
        :answerSdpValue
        :offerSdpValue
        :isLoading
        :loadingAction
      />

      <!-- SDP Exchange Section -->
      <div class="grid grid-cols-1 lg:grid-cols-2 gap-6">
        <!-- Offer SDP Panel -->
        <Card class="tactical-card transition-all duration-300 hover:shadow-xl">
          <CardHeader
            class="bg-gradient-to-r from-[var(--tactical-electric)]/5 to-[var(--tactical-electric-light)]/5"
          >
            <CardTitle class="flex items-center justify-between">
              <div class="flex items-center gap-3">
                <div class="tactical-gradient p-2 rounded-md">
                  <FileText class="h-5 w-5 text-white" />
                </div>
                <div>
                  <div
                    class="text-[var(--tactical-primary)] font-bold tracking-wide"
                  >
                    OFFER SDP
                  </div>
                  <div
                    class="text-xs text-[var(--tactical-electric)] font-medium uppercase tracking-wider"
                  >
                    Connection Initiation
                  </div>
                </div>
              </div>
              <div class="flex gap-2">
                <TooltipProvider>
                  <Tooltip>
                    <TooltipTrigger asChild>
                      <Button
                        variant="ghost"
                        size="sm"
                        @click="copyToClipboard('offer')"
                        class="tactical-button text-[var(--tactical-electric)] hover:text-[var(--tactical-electric-light)] hover:bg-[var(--tactical-electric)]/10"
                      >
                        <Copy class="h-4 w-4" />
                      </Button>
                    </TooltipTrigger>
                    <TooltipContent
                      class="bg-[var(--tactical-primary)] text-white border-[var(--tactical-electric)]"
                    >
                      <p class="font-medium">Copy to secure clipboard</p>
                    </TooltipContent>
                  </Tooltip>
                </TooltipProvider>
                <TooltipProvider>
                  <Tooltip>
                    <TooltipTrigger asChild>
                      <Button
                        variant="ghost"
                        size="sm"
                        @click="clearSdp('offer')"
                        class="tactical-button text-red-500 hover:text-red-400 hover:bg-red-500/10"
                      >
                        <Trash2 class="h-4 w-4" />
                      </Button>
                    </TooltipTrigger>
                    <TooltipContent class="bg-red-600 text-white">
                      <p class="font-medium">Clear protocol data</p>
                    </TooltipContent>
                  </Tooltip>
                </TooltipProvider>
              </div>
            </CardTitle>
            <CardDescription class="text-[var(--tactical-accent)] font-medium">
              Session Description Protocol for connection establishment
            </CardDescription>
          </CardHeader>
          <CardContent class="relative">
            <!-- Tactical Terminal Style -->
            <div class="relative">
              <div
                class="absolute top-3 left-3 z-10 bg-black/70 px-2 py-1 rounded text-xs font-mono text-[var(--tactical-electric)]"
              >
                OFFER_SDP
              </div>
              <Textarea
                ref="offerSdp"
                v-model="offerSdpValue"
                placeholder="// Waiting for offer SDP generation...
// Protocol will appear here after creating connection offer
// Format: Session Description Protocol (RFC 4566)"
                class="min-h-[180px] font-mono text-xs resize-none bg-black/5 border-[var(--tactical-primary)]/30 focus:border-[var(--tactical-electric)] focus:ring-[var(--tactical-electric)] text-[var(--tactical-primary)] placeholder-[var(--tactical-accent)]/60 pt-8"
              />
            </div>

            <!-- Status Indicator -->
            <div class="mt-3 flex items-center justify-between text-xs">
              <div class="flex items-center space-x-2">
                <div
                  class="w-2 h-2 rounded-full"
                  :class="
                    offerSdpValue ? 'bg-green-500 animate-pulse' : 'bg-gray-400'
                  "
                ></div>
                <span
                  class="text-[var(--tactical-primary)] font-medium uppercase tracking-wide"
                >
                  {{ offerSdpValue ? "SDP Ready" : "Standby" }}
                </span>
              </div>
              <div class="text-[var(--tactical-accent)] font-mono">
                {{
                  offerSdpValue ? `${offerSdpValue.length} BYTES` : "NO DATA"
                }}
              </div>
            </div>
          </CardContent>
        </Card>

        <!-- Answer SDP Panel -->
        <Card class="tactical-card transition-all duration-300 hover:shadow-xl">
          <CardHeader
            class="bg-gradient-to-r from-[var(--tactical-secondary)]/5 to-[var(--tactical-accent)]/5"
          >
            <CardTitle class="flex items-center justify-between">
              <div class="flex items-center gap-3">
                <div
                  class="bg-gradient-to-br from-[var(--tactical-secondary)] to-[var(--tactical-accent)] p-2 rounded-md"
                >
                  <FileText class="h-5 w-5 text-white" />
                </div>
                <div>
                  <div
                    class="text-[var(--tactical-primary)] font-bold tracking-wide"
                  >
                    ANSWER SDP
                  </div>
                  <div
                    class="text-xs text-[var(--tactical-electric)] font-medium uppercase tracking-wider"
                  >
                    Connection Response
                  </div>
                </div>
              </div>
              <div class="flex gap-2">
                <TooltipProvider>
                  <Tooltip>
                    <TooltipTrigger asChild>
                      <Button
                        variant="ghost"
                        size="sm"
                        @click="copyToClipboard('answer')"
                        class="tactical-button text-[var(--tactical-electric)] hover:text-[var(--tactical-electric-light)] hover:bg-[var(--tactical-electric)]/10"
                      >
                        <Copy class="h-4 w-4" />
                      </Button>
                    </TooltipTrigger>
                    <TooltipContent
                      class="bg-[var(--tactical-primary)] text-white border-[var(--tactical-electric)]"
                    >
                      <p class="font-medium">Copy to secure clipboard</p>
                    </TooltipContent>
                  </Tooltip>
                </TooltipProvider>
                <TooltipProvider>
                  <Tooltip>
                    <TooltipTrigger asChild>
                      <Button
                        variant="ghost"
                        size="sm"
                        @click="clearSdp('answer')"
                        class="tactical-button text-red-500 hover:text-red-400 hover:bg-red-500/10"
                      >
                        <Trash2 class="h-4 w-4" />
                      </Button>
                    </TooltipTrigger>
                    <TooltipContent class="bg-red-600 text-white">
                      <p class="font-medium">Clear protocol data</p>
                    </TooltipContent>
                  </Tooltip>
                </TooltipProvider>
              </div>
            </CardTitle>
            <CardDescription class="text-[var(--tactical-accent)] font-medium">
              Peer response protocol for handshake completion
            </CardDescription>
          </CardHeader>
          <CardContent class="relative">
            <!-- Tactical Terminal Style -->
            <div class="relative">
              <div
                class="absolute top-3 left-3 z-10 bg-black/70 px-2 py-1 rounded text-xs font-mono text-[var(--tactical-electric)]"
              >
                ANSWER_SDP
              </div>
              <Textarea
                ref="answerSdp"
                v-model="answerSdpValue"
                placeholder="// Waiting for answer SDP generation...
// Response protocol will appear here after creating answer
// Format: Session Description Protocol (RFC 4566)"
                class="min-h-[180px] font-mono text-xs resize-none bg-black/5 border-[var(--tactical-primary)]/30 focus:border-[var(--tactical-electric)] focus:ring-[var(--tactical-electric)] text-[var(--tactical-primary)] placeholder-[var(--tactical-accent)]/60 pt-8"
              />
            </div>

            <!-- Status Indicator -->
            <div class="mt-3 flex items-center justify-between text-xs">
              <div class="flex items-center space-x-2">
                <div
                  class="w-2 h-2 rounded-full"
                  :class="
                    answerSdpValue
                      ? 'bg-green-500 animate-pulse'
                      : 'bg-gray-400'
                  "
                ></div>
                <span
                  class="text-[var(--tactical-primary)] font-medium uppercase tracking-wide"
                >
                  {{ answerSdpValue ? "SDP Ready" : "Standby" }}
                </span>
              </div>
              <div class="text-[var(--tactical-accent)] font-mono">
                {{
                  answerSdpValue ? `${answerSdpValue.length} BYTES` : "NO DATA"
                }}
              </div>
            </div>
          </CardContent>
        </Card>
      </div>
    </div>
  </div>
</template>

<script lang="ts" setup>
import { ref, computed, onMounted, onUnmounted, nextTick } from "vue";
import { FileText, Copy, Trash2, AlertCircle, X } from "lucide-vue-next";

const offerSdp = ref<HTMLTextAreaElement>();
const answerSdp = ref<HTMLTextAreaElement>();

const offerSdpValue = ref<string>("");
const answerSdpValue = ref<string>("");

const peerConnection = ref<RTCPeerConnection>();
const localStream = ref<MediaStream>();
const remoteStream = ref<MediaStream>();

const isLoading = ref(false);
const loadingAction = ref<"offer" | "answer" | "add" | null>(null);
const error = ref<string | null>(null);
const connectionStatus = ref<
  "disconnected" | "connecting" | "connected" | "failed"
>("disconnected");

// Computed properties
const connectionBadgeVariant = computed(() => {
  switch (connectionStatus.value) {
    case "connected":
      return "default";
    case "connecting":
      return "secondary";
    case "failed":
      return "destructive";
    default:
      return "outline";
  }
});

// Utility methods
const setError = (message: string) => {
  error.value = message;
  setTimeout(() => {
    error.value = null;
  }, 5000);
};

const dismissError = () => {
  error.value = null;
};

const copyToClipboard = async (type: "offer" | "answer") => {
  try {
    const value = type === "offer" ? offerSdpValue.value : answerSdpValue.value;
    if (value) {
      await navigator.clipboard.writeText(value);
      // You could add a toast notification here
      console.log(`${type} SDP copied to clipboard`);
    }
  } catch (err) {
    console.error("Failed to copy to clipboard:", err);
    setError("Failed to copy to clipboard");
  }
};

const clearSdp = (type: "offer" | "answer") => {
  if (type === "offer") {
    offerSdpValue.value = "";
  } else {
    answerSdpValue.value = "";
  }
};

const updateConnectionStatus = () => {
  if (!peerConnection.value) {
    connectionStatus.value = "disconnected";
    return;
  }

  const state = peerConnection.value.connectionState;
  switch (state) {
    case "connected":
      connectionStatus.value = "connected";
      break;
    case "connecting":
    case "new":
      connectionStatus.value = "connecting";
      break;
    case "failed":
    case "closed":
      connectionStatus.value = "failed";
      break;
    default:
      connectionStatus.value = "disconnected";
  }
};

const init = async () => {
  try {
    connectionStatus.value = "connecting";

    peerConnection.value = new RTCPeerConnection();

    peerConnection.value.onconnectionstatechange = () => {
      updateConnectionStatus();
    };

    localStream.value = await navigator.mediaDevices.getUserMedia({
      video: true,
      audio: false,
    });

    remoteStream.value = new MediaStream();

    localStream.value.getTracks().forEach((track) => {
      if (peerConnection.value && localStream.value) {
        peerConnection.value.addTrack(track, localStream.value);
      }
    });

    peerConnection.value.ontrack = (event) => {
      if (event.streams && event.streams[0]) {
        event.streams[0].getTracks().forEach((track) => {
          if (remoteStream.value) {
            remoteStream.value.addTrack(track);
          }
        });
      }
    };

    connectionStatus.value = "disconnected";
    console.log("WebRTC initialized successfully");
  } catch (err) {
    console.error("Error initializing WebRTC:", err);
    setError("Failed to initialize camera. Please check permissions.");
    connectionStatus.value = "failed";
  }
};

const createOffer = async () => {
  try {
    if (!peerConnection.value) return;

    isLoading.value = true;
    loadingAction.value = "offer";
    connectionStatus.value = "connecting";

    peerConnection.value.onicecandidate = async (event) => {
      if (event.candidate) {
        console.log("ICE candidate generated:", event.candidate);
        if (peerConnection.value?.localDescription) {
          offerSdpValue.value = JSON.stringify(
            peerConnection.value.localDescription
          );
        }
      }
    };

    const offer = await peerConnection.value.createOffer();
    await peerConnection.value.setLocalDescription(offer);
    console.log("Offer created successfully");
  } catch (err) {
    console.error("Error creating offer:", err);
    setError("Failed to create offer. Please try again.");
    connectionStatus.value = "failed";
  } finally {
    isLoading.value = false;
    loadingAction.value = null;
  }
};

const createAnswer = async () => {
  try {
    if (!peerConnection.value) return;

    if (!offerSdpValue.value) {
      setError("Please create an offer first");
      return;
    }

    isLoading.value = true;
    loadingAction.value = "answer";
    connectionStatus.value = "connecting";

    const offer = JSON.parse(offerSdpValue.value);

    peerConnection.value.onicecandidate = async (event) => {
      if (event.candidate) {
        console.log("Adding answer candidate...:", event.candidate);
        if (peerConnection.value?.localDescription) {
          answerSdpValue.value = JSON.stringify(
            peerConnection.value.localDescription
          );
        }
      }
    };

    await peerConnection.value.setRemoteDescription(offer);
    const answer = await peerConnection.value.createAnswer();
    await peerConnection.value.setLocalDescription(answer);
    console.log("Answer created successfully");
  } catch (err) {
    console.error("Error creating answer:", err);
    setError("Failed to create answer. Please check the offer SDP.");
    connectionStatus.value = "failed";
  } finally {
    isLoading.value = false;
    loadingAction.value = null;
  }
};

const addAnswer = async () => {
  try {
    if (!peerConnection.value) return;

    console.log("Add answer triggered");

    if (!answerSdpValue.value) {
      setError("Please create an answer first");
      return;
    }

    isLoading.value = true;
    loadingAction.value = "add";

    const answer = JSON.parse(answerSdpValue.value);
    console.log("answer:", answer);

    if (!peerConnection.value.currentRemoteDescription) {
      await peerConnection.value.setRemoteDescription(answer);
      console.log("Answer added successfully", remoteStream.value?.getTracks());
      connectionStatus.value = "connected";
    } else {
      console.log("Remote description already set");
    }
  } catch (err) {
    console.error("Error adding answer:", err);
    setError("Failed to add answer. Please check the answer SDP.");
    connectionStatus.value = "failed";
  } finally {
    isLoading.value = false;
    loadingAction.value = null;
  }
};

onMounted(() => {
  init();
});

onUnmounted(() => {
  if (localStream.value) {
    localStream.value.getTracks().forEach((track) => track.stop());
  }
  if (peerConnection.value) {
    peerConnection.value.close();
  }
});

useSeoMeta({
  title: "Camouflage - Simple WebRTC",
});
</script>
