<template>
  <div class="container mx-auto p-6 max-w-6xl space-y-8">
    <!-- Header Section -->
    <div class="space-y-2">
      <h1
        class="scroll-m-20 text-4xl font-extrabold tracking-tight lg:text-5xl"
      >
        WebRTC Peer Connection
      </h1>
      <p class="text-xl text-muted-foreground">
        Manage SDP exchange for direct peer-to-peer communication
      </p>
      <div class="flex items-center gap-2">
        <Badge :variant="connectionBadgeVariant">
          {{ connectionStatus }}
        </Badge>
        <TooltipProvider>
          <Tooltip>
            <TooltipTrigger>
              <HelpCircle class="h-4 w-4 text-muted-foreground" />
            </TooltipTrigger>
            <TooltipContent>
              <p>Connection status updates in real-time</p>
            </TooltipContent>
          </Tooltip>
        </TooltipProvider>
      </div>
    </div>

    <!-- Error Alert -->
    <Alert
      v-if="error"
      variant="destructive"
      class="transition-all duration-300"
    >
      <AlertCircle class="h-4 w-4" />
      <AlertTitle>Connection Error</AlertTitle>
      <AlertDescription class="flex items-center justify-between">
        {{ error }}
        <Button variant="ghost" size="sm" @click="dismissError">
          <X class="h-4 w-4" />
        </Button>
      </AlertDescription>
    </Alert>

    <!-- Video Section -->
    <div class="grid grid-cols-1 lg:grid-cols-2 gap-6">
      <Card class="overflow-hidden transition-all duration-300 hover:shadow-lg">
        <CardHeader class="pb-3">
          <CardTitle class="flex items-center gap-2">
            <Video class="h-5 w-5" />
            Local Video (You)
          </CardTitle>
          <CardDescription>Your camera feed</CardDescription>
        </CardHeader>
        <CardContent class="pb-6">
          <div
            class="relative bg-gradient-to-br from-muted to-muted-foreground/20 rounded-lg overflow-hidden aspect-video"
          >
            <video
              ref="localVideo"
              autoplay
              playsinline
              muted
              class="w-full h-full object-cover"
            />
            <div
              v-if="!localStream"
              class="absolute inset-0 flex items-center justify-center"
            >
              <Skeleton class="w-full h-full" />
            </div>
          </div>
        </CardContent>
      </Card>

      <Card class="overflow-hidden transition-all duration-300 hover:shadow-lg">
        <CardHeader class="pb-3">
          <CardTitle class="flex items-center gap-2">
            <Users class="h-5 w-5" />
            Remote Video (Peer)
          </CardTitle>
          <CardDescription>Connected peer's camera feed</CardDescription>
        </CardHeader>
        <CardContent class="pb-6">
          <div
            class="relative bg-gradient-to-br from-muted to-muted-foreground/20 rounded-lg overflow-hidden aspect-video"
          >
            <video
              ref="remoteVideo"
              autoplay
              playsinline
              class="w-full h-full object-cover"
            />
            <div
              v-if="!remoteStream || remoteStream.getTracks().length === 0"
              class="absolute inset-0 flex items-center justify-center"
            >
              <div class="text-center space-y-2">
                <UserX class="h-12 w-12 mx-auto text-muted-foreground" />
                <p class="text-sm text-muted-foreground">
                  Waiting for peer connection
                </p>
              </div>
            </div>
          </div>
        </CardContent>
      </Card>
    </div>

    <!-- Controls Section -->
    <Card>
      <CardHeader>
        <CardTitle class="flex items-center gap-2">
          <Settings class="h-5 w-5" />
          Connection Controls
        </CardTitle>
        <CardDescription>
          Create and exchange SDP offers and answers to establish peer
          connection
        </CardDescription>
      </CardHeader>
      <CardContent>
        <div class="grid grid-cols-1 md:grid-cols-3 gap-4">
          <TooltipProvider>
            <Tooltip>
              <TooltipTrigger asChild>
                <Button
                  @click="createOffer"
                  :disabled="isLoading"
                  class="transition-all duration-200"
                  size="lg"
                >
                  <Phone class="h-4 w-4 mr-2" />
                  {{
                    isLoading && loadingAction === "offer"
                      ? "Creating..."
                      : "Create Offer"
                  }}
                </Button>
              </TooltipTrigger>
              <TooltipContent>
                <p>Initiate a new peer connection</p>
              </TooltipContent>
            </Tooltip>
          </TooltipProvider>

          <TooltipProvider>
            <Tooltip>
              <TooltipTrigger asChild>
                <Button
                  @click="createAnswer"
                  :disabled="isLoading || !offerSdpValue"
                  variant="secondary"
                  class="transition-all duration-200"
                  size="lg"
                >
                  <PhoneCall class="h-4 w-4 mr-2" />
                  {{
                    isLoading && loadingAction === "answer"
                      ? "Creating..."
                      : "Create Answer"
                  }}
                </Button>
              </TooltipTrigger>
              <TooltipContent>
                <p>Respond to an incoming offer</p>
              </TooltipContent>
            </Tooltip>
          </TooltipProvider>

          <TooltipProvider>
            <Tooltip>
              <TooltipTrigger asChild>
                <Button
                  @click="addAnswer"
                  :disabled="isLoading || !answerSdpValue"
                  variant="outline"
                  class="transition-all duration-200"
                  size="lg"
                >
                  <Check class="h-4 w-4 mr-2" />
                  {{
                    isLoading && loadingAction === "add"
                      ? "Adding..."
                      : "Add Answer"
                  }}
                </Button>
              </TooltipTrigger>
              <TooltipContent>
                <p>Complete the connection with answer SDP</p>
              </TooltipContent>
            </Tooltip>
          </TooltipProvider>
        </div>
      </CardContent>
    </Card>

    <Separator />

    <!-- SDP Exchange Section -->
    <div class="grid grid-cols-1 lg:grid-cols-2 gap-6">
      <Card class="transition-all duration-300 hover:shadow-md">
        <CardHeader>
          <CardTitle class="flex items-center justify-between">
            <div class="flex items-center gap-2">
              <FileText class="h-5 w-5" />
              Offer SDP
            </div>
            <div class="flex gap-2">
              <TooltipProvider>
                <Tooltip>
                  <TooltipTrigger asChild>
                    <Button
                      variant="ghost"
                      size="sm"
                      @click="copyToClipboard('offer')"
                    >
                      <Copy class="h-4 w-4" />
                    </Button>
                  </TooltipTrigger>
                  <TooltipContent>
                    <p>Copy to clipboard</p>
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
                    >
                      <Trash2 class="h-4 w-4" />
                    </Button>
                  </TooltipTrigger>
                  <TooltipContent>
                    <p>Clear content</p>
                  </TooltipContent>
                </Tooltip>
              </TooltipProvider>
            </div>
          </CardTitle>
          <CardDescription>Connection offer details for peer</CardDescription>
        </CardHeader>
        <CardContent>
          <Textarea
            ref="offerSdp"
            v-model="offerSdpValue"
            placeholder="Offer SDP will appear here after creating an offer..."
            class="min-h-[160px] font-mono text-xs resize-none transition-all duration-200 focus:ring-2"
          />
        </CardContent>
      </Card>

      <Card class="transition-all duration-300 hover:shadow-md">
        <CardHeader>
          <CardTitle class="flex items-center justify-between">
            <div class="flex items-center gap-2">
              <FileText class="h-5 w-5" />
              Answer SDP
            </div>
            <div class="flex gap-2">
              <TooltipProvider>
                <Tooltip>
                  <TooltipTrigger asChild>
                    <Button
                      variant="ghost"
                      size="sm"
                      @click="copyToClipboard('answer')"
                    >
                      <Copy class="h-4 w-4" />
                    </Button>
                  </TooltipTrigger>
                  <TooltipContent>
                    <p>Copy to clipboard</p>
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
                    >
                      <Trash2 class="h-4 w-4" />
                    </Button>
                  </TooltipTrigger>
                  <TooltipContent>
                    <p>Clear content</p>
                  </TooltipContent>
                </Tooltip>
              </TooltipProvider>
            </div>
          </CardTitle>
          <CardDescription>Response to connection offer</CardDescription>
        </CardHeader>
        <CardContent>
          <Textarea
            ref="answerSdp"
            v-model="answerSdpValue"
            placeholder="Answer SDP will appear here after creating an answer..."
            class="min-h-[160px] font-mono text-xs resize-none transition-all duration-200 focus:ring-2"
          />
        </CardContent>
      </Card>
    </div>
  </div>
</template>

<script lang="ts" setup>
import { ref, computed, onMounted, onUnmounted } from "vue";
import {
  Card,
  CardContent,
  CardDescription,
  CardHeader,
  CardTitle,
} from "@/components/ui/card";
import { Button } from "@/components/ui/button";
import { Textarea } from "@/components/ui/textarea";
import { Alert, AlertDescription, AlertTitle } from "@/components/ui/alert";
import { Badge } from "@/components/ui/badge";
import { Separator } from "@/components/ui/separator";
import { Skeleton } from "@/components/ui/skeleton";
import {
  Tooltip,
  TooltipContent,
  TooltipProvider,
  TooltipTrigger,
} from "@/components/ui/tooltip";
import {
  Video,
  Users,
  Settings,
  Phone,
  PhoneCall,
  Check,
  FileText,
  Copy,
  Trash2,
  AlertCircle,
  X,
  HelpCircle,
  UserX,
} from "lucide-vue-next";

// Reactive state
const localVideo = ref<HTMLVideoElement>();
const remoteVideo = ref<HTMLVideoElement>();
const offerSdp = ref<HTMLTextAreaElement>();
const answerSdp = ref<HTMLTextAreaElement>();

// SDP values as reactive refs for proper two-way binding
const offerSdpValue = ref<string>("");
const answerSdpValue = ref<string>("");

const peerConnection = ref<RTCPeerConnection>();
const localStream = ref<MediaStream>();
const remoteStream = ref<MediaStream>();

// UI state
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
  }, 5000); // Auto-dismiss after 5 seconds
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

    // Initialize peer connection
    peerConnection.value = new RTCPeerConnection();

    // Monitor connection state changes
    peerConnection.value.onconnectionstatechange = () => {
      updateConnectionStatus();
    };

    // Get user media
    localStream.value = await navigator.mediaDevices.getUserMedia({
      video: true,
      audio: false,
    });

    // Initialize remote stream
    remoteStream.value = new MediaStream();

    // Set video sources
    if (localVideo.value && localStream.value) {
      localVideo.value.srcObject = localStream.value;
    }
    if (remoteVideo.value) {
      remoteVideo.value.srcObject = remoteStream.value;
    }

    // Add local tracks to peer connection
    localStream.value.getTracks().forEach((track) => {
      if (peerConnection.value && localStream.value) {
        peerConnection.value.addTrack(track, localStream.value);
      }
    });

    // Handle incoming tracks
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
      console.log("Answer added successfully");
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
