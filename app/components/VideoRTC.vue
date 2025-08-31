<template>
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
    }
  },
  { immediate: true }
);
</script>
