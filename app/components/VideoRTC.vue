<template>
  <div class="grid grid-cols-1 lg:grid-cols-2 gap-6">
    <!-- Local Video Panel -->
    <Card
      class="tactical-card overflow-hidden transition-all duration-300 hover:shadow-xl group"
    >
      <CardHeader
        class="pb-3 bg-gradient-to-r from-[var(--tactical-primary)]/5 to-[var(--tactical-secondary)]/5"
      >
        <CardTitle class="flex items-center justify-between">
          <div class="flex items-center gap-3">
            <div class="tactical-gradient p-2 rounded-md">
              <Video class="h-5 w-5 text-white" />
            </div>
            <div>
              <div
                class="text-[var(--tactical-primary)] font-bold tracking-wide"
              >
                LOCAL FEED
              </div>
              <div
                class="text-xs text-[var(--tactical-electric)] font-medium uppercase tracking-wider"
              >
                Operator Video
              </div>
            </div>
          </div>
          <!-- Signal Strength Indicator -->
          <div class="flex items-center space-x-1">
            <div
              class="w-1 h-3 bg-[var(--tactical-electric)] rounded-full"
            ></div>
            <div
              class="w-1 h-4 bg-[var(--tactical-electric)] rounded-full"
            ></div>
            <div
              class="w-1 h-5 bg-[var(--tactical-electric)] rounded-full"
            ></div>
            <div
              class="w-1 h-6 bg-[var(--tactical-electric)] rounded-full opacity-50"
            ></div>
          </div>
        </CardTitle>
        <CardDescription class="text-[var(--tactical-accent)] font-medium">
          Your secure camera transmission
        </CardDescription>
      </CardHeader>
      <CardContent class="pb-6 relative">
        <!-- Video Container with Tactical Styling -->
        <div
          class="relative tactical-border bg-black rounded-lg overflow-hidden aspect-video group-hover:shadow-lg transition-all duration-300"
        >
          <!-- Tactical Grid Overlay -->
          <div class="absolute inset-0 z-10 pointer-events-none opacity-20">
            <svg
              class="w-full h-full"
              viewBox="0 0 100 100"
              preserveAspectRatio="none"
            >
              <defs>
                <pattern
                  id="grid"
                  width="10"
                  height="10"
                  patternUnits="userSpaceOnUse"
                >
                  <path
                    d="M 10 0 L 0 0 0 10"
                    fill="none"
                    stroke="var(--tactical-electric)"
                    stroke-width="0.5"
                  />
                </pattern>
              </defs>
              <rect width="100%" height="100%" fill="url(#grid)" />
            </svg>
          </div>

          <!-- Corner Indicators -->
          <div class="absolute top-2 left-2 z-20">
            <div
              class="w-4 h-4 border-l-2 border-t-2 border-[var(--tactical-electric)]"
            ></div>
          </div>
          <div class="absolute top-2 right-2 z-20">
            <div
              class="w-4 h-4 border-r-2 border-t-2 border-[var(--tactical-electric)]"
            ></div>
          </div>
          <div class="absolute bottom-2 left-2 z-20">
            <div
              class="w-4 h-4 border-l-2 border-b-2 border-[var(--tactical-electric)]"
            ></div>
          </div>
          <div class="absolute bottom-2 right-2 z-20">
            <div
              class="w-4 h-4 border-r-2 border-b-2 border-[var(--tactical-electric)]"
            ></div>
          </div>

          <!-- Status Overlay -->
          <div
            class="absolute top-3 left-3 z-30 bg-black/70 px-2 py-1 rounded text-xs font-mono text-[var(--tactical-electric)]"
          >
            LOCAL
          </div>

          <video
            ref="localVideo"
            autoplay
            playsinline
            muted
            class="w-full h-full object-cover relative z-0"
          />
          <div
            v-if="!localStream"
            class="absolute inset-0 flex items-center justify-center bg-[var(--tactical-primary)]/10"
          >
            <div class="text-center space-y-4">
              <Skeleton class="w-full h-full bg-[var(--tactical-primary)]/20" />
              <div class="absolute inset-0 flex items-center justify-center">
                <div
                  class="text-[var(--tactical-primary)] font-bold tracking-wide"
                >
                  INITIALIZING FEED...
                </div>
              </div>
            </div>
          </div>
        </div>

        <!-- Feed Status Bar -->
        <div class="mt-3 flex items-center justify-between text-xs">
          <div class="flex items-center space-x-2">
            <div class="w-2 h-2 bg-green-500 rounded-full animate-pulse"></div>
            <span
              class="text-[var(--tactical-primary)] font-medium uppercase tracking-wide"
            >
              Feed Active
            </span>
          </div>
          <div class="text-[var(--tactical-accent)] font-mono">
            RES: 1920x1080
          </div>
        </div>
      </CardContent>
    </Card>

    <!-- Remote Video Panel -->
    <Card
      class="tactical-card overflow-hidden transition-all duration-300 hover:shadow-xl group"
    >
      <CardHeader
        class="pb-3 bg-gradient-to-r from-[var(--tactical-secondary)]/5 to-[var(--tactical-tertiary)]/5"
      >
        <CardTitle class="flex items-center justify-between">
          <div class="flex items-center gap-3">
            <div
              class="bg-gradient-to-br from-[var(--tactical-secondary)] to-[var(--tactical-accent)] p-2 rounded-md"
            >
              <Users class="h-5 w-5 text-white" />
            </div>
            <div>
              <div
                class="text-[var(--tactical-primary)] font-bold tracking-wide"
              >
                REMOTE FEED
              </div>
              <div
                class="text-xs text-[var(--tactical-electric)] font-medium uppercase tracking-wider"
              >
                Peer Connection
              </div>
            </div>
          </div>
          <!-- Connection Quality Indicator -->
          <div class="flex items-center space-x-1">
            <div
              class="w-1 h-3 bg-[var(--tactical-electric)] rounded-full"
              :class="props.remoteStream ? '' : 'opacity-20'"
            ></div>
            <div
              class="w-1 h-4 bg-[var(--tactical-electric)] rounded-full"
              :class="props.remoteStream ? '' : 'opacity-20'"
            ></div>
            <div
              class="w-1 h-5 bg-[var(--tactical-electric)] rounded-full"
              :class="props.remoteStream ? '' : 'opacity-20'"
            ></div>
            <div
              class="w-1 h-6 bg-[var(--tactical-electric)] rounded-full"
              :class="props.remoteStream ? 'opacity-50' : 'opacity-10'"
            ></div>
          </div>
        </CardTitle>
        <CardDescription class="text-[var(--tactical-accent)] font-medium">
          Incoming secure transmission
        </CardDescription>
      </CardHeader>
      <CardContent class="pb-6 relative">
        <!-- Video Container with Tactical Styling -->
        <div
          class="relative tactical-border bg-black rounded-lg overflow-hidden aspect-video group-hover:shadow-lg transition-all duration-300"
        >
          <!-- Tactical Grid Overlay -->
          <div class="absolute inset-0 z-10 pointer-events-none opacity-20">
            <svg
              class="w-full h-full"
              viewBox="0 0 100 100"
              preserveAspectRatio="none"
            >
              <defs>
                <pattern
                  id="grid-remote"
                  width="10"
                  height="10"
                  patternUnits="userSpaceOnUse"
                >
                  <path
                    d="M 10 0 L 0 0 0 10"
                    fill="none"
                    stroke="var(--tactical-electric)"
                    stroke-width="0.5"
                  />
                </pattern>
              </defs>
              <rect width="100%" height="100%" fill="url(#grid-remote)" />
            </svg>
          </div>

          <!-- Corner Indicators -->
          <div class="absolute top-2 left-2 z-20">
            <div
              class="w-4 h-4 border-l-2 border-t-2 border-[var(--tactical-electric)]"
            ></div>
          </div>
          <div class="absolute top-2 right-2 z-20">
            <div
              class="w-4 h-4 border-r-2 border-t-2 border-[var(--tactical-electric)]"
            ></div>
          </div>
          <div class="absolute bottom-2 left-2 z-20">
            <div
              class="w-4 h-4 border-l-2 border-b-2 border-[var(--tactical-electric)]"
            ></div>
          </div>
          <div class="absolute bottom-2 right-2 z-20">
            <div
              class="w-4 h-4 border-r-2 border-b-2 border-[var(--tactical-electric)]"
            ></div>
          </div>

          <!-- Status Overlay -->
          <div
            class="absolute top-3 left-3 z-30 bg-black/70 px-2 py-1 rounded text-xs font-mono text-[var(--tactical-electric)]"
          >
            REMOTE
          </div>

          <video
            ref="remoteVideo"
            autoplay
            playsinline
            class="w-full h-full object-cover relative z-0"
          />

          <!-- Waiting State -->
          <div
            v-if="showWaitingState"
            class="absolute inset-0 flex items-center justify-center bg-[var(--tactical-primary)]/10"
          >
            <div class="text-center space-y-4">
              <div
                class="text-[var(--tactical-primary)] text-2xl font-bold tracking-wide mb-2"
              >
                AWAITING CONNECTION
              </div>
              <div class="flex justify-center space-x-1">
                <div
                  class="w-3 h-3 bg-[var(--tactical-electric)] rounded-full animate-bounce"
                  style="animation-delay: 0ms"
                ></div>
                <div
                  class="w-3 h-3 bg-[var(--tactical-electric)] rounded-full animate-bounce"
                  style="animation-delay: 150ms"
                ></div>
                <div
                  class="w-3 h-3 bg-[var(--tactical-electric)] rounded-full animate-bounce"
                  style="animation-delay: 300ms"
                ></div>
              </div>
              <div
                class="text-[var(--tactical-accent)] text-sm font-medium uppercase tracking-wider"
              >
                Standby for incoming transmission
              </div>
            </div>
          </div>
        </div>

        <!-- Connection Status Bar -->
        <div class="mt-3 flex items-center justify-between text-xs">
          <div class="flex items-center space-x-2">
            <div
              class="w-2 h-2 rounded-full"
              :class="
                props.remoteStream ? 'bg-green-500 animate-pulse' : 'bg-red-500'
              "
            ></div>
            <span
              class="text-[var(--tactical-primary)] font-medium uppercase tracking-wide"
            >
              {{ props.remoteStream ? "Connected" : "Disconnected" }}
            </span>
          </div>
          <div class="text-[var(--tactical-accent)] font-mono">
            {{ props.remoteStream ? "ENCRYPTED" : "STANDBY" }}
          </div>
        </div>
      </CardContent>
    </Card>
  </div>
</template>

<script setup lang="ts">
import { Video, Users, UserX } from "lucide-vue-next";
import { watch, nextTick } from "vue";

const props = defineProps<{
  remoteStream: MediaStream | undefined;
  localStream: MediaStream | undefined;
}>();

const localVideo = ref<HTMLVideoElement>();
const remoteVideo = ref<HTMLVideoElement>();
const hasRemoteTracks = ref(false);

const showWaitingState = computed(() => {
  return !props.remoteStream || !hasRemoteTracks.value;
});

watch(
  () => props.localStream,
  async (newStream) => {
    await nextTick();
    if (localVideo.value && newStream) {
      console.log("Setting local video stream in VideoRTC component");
      localVideo.value.srcObject = newStream;
    }
  },
  { immediate: true }
);

watch(
  () => props.remoteStream,
  async (newStream) => {
    await nextTick();
    if (remoteVideo.value && newStream) {
      console.log("Setting remote video stream in VideoRTC component");
      remoteVideo.value.srcObject = newStream;
      hasRemoteTracks.value = true;
    }
  },
  { immediate: true }
);
</script>
