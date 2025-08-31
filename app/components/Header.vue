<template>
  <div class="space-y-2">
    <h1 class="scroll-m-20 text-4xl font-extrabold tracking-tight lg:text-5xl">
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
</template>

<script setup lang="ts">
import { HelpCircle } from "lucide-vue-next";

defineProps<{
  connectionBadgeVariant:
    | "default"
    | "secondary"
    | "destructive"
    | "outline"
    | null
    | undefined;
  connectionStatus: "disconnected" | "connecting" | "connected" | "failed";
}>();
</script>
