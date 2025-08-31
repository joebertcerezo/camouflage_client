<template>
  <div class="relative">
    <!-- Tactical Background Pattern -->
    <div class="absolute inset-0 tactical-pattern opacity-5 -z-10"></div>

    <!-- Main Header Container -->
    <div class="tactical-card p-6 space-y-4">
      <!-- Branding Section -->
      <div class="flex items-center justify-between">
        <div class="flex items-center space-x-4">
          <!-- Camouflage Logo/Icon -->
          <div class="tactical-gradient p-3 rounded-lg">
            <svg
              class="w-8 h-8 text-white"
              fill="currentColor"
              viewBox="0 0 24 24"
            >
              <path
                d="M12 2L2 7l10 5 10-5-10-5zM2 17l10 5 10-5M2 12l10 5 10-5"
              />
            </svg>
          </div>

          <!-- Title Section -->
          <div>
            <h1
              class="text-2xl md:text-4xl font-bold tactical-text-shadow tracking-wide"
            >
              <span class="text-[var(--tactical-primary)]">CAMOUFLAGE</span>
            </h1>
            <div
              class="text-xs md:text-sm font-semibold tracking-wider text-[var(--tactical-electric)] uppercase"
            >
              Secure Communication Platform
            </div>
          </div>
        </div>

        <!-- Connection Status Dashboard -->
        <div class="flex items-center space-x-3">
          <div class="hidden md:block text-right">
            <div
              class="text-xs font-medium text-[var(--tactical-accent)] uppercase tracking-wide"
            >
              Connection Status
            </div>
            <div class="text-sm font-bold text-[var(--tactical-primary)]">
              OPERATIONAL
            </div>
          </div>

          <!-- Status Indicator -->
          <div class="tactical-border p-1">
            <Badge
              :variant="connectionBadgeVariant"
              :class="[
                'px-3 py-1 font-bold text-xs tracking-wide uppercase',
                connectionStatus === 'connected'
                  ? 'connection-pulse bg-[var(--tactical-electric)] text-white'
                  : connectionStatus === 'connecting'
                  ? 'bg-yellow-500 text-white animate-pulse'
                  : connectionStatus === 'failed'
                  ? 'bg-red-500 text-white'
                  : 'bg-[var(--tactical-accent)] text-white',
              ]"
            >
              <div class="flex items-center space-x-2">
                <div class="w-2 h-2 rounded-full bg-current"></div>
                <span>{{ connectionStatus.toUpperCase() }}</span>
              </div>
            </Badge>
          </div>
        </div>
      </div>

      <!-- Mission Statement -->
      <div class="border-t border-[var(--tactical-primary)]/20 pt-4">
        <h2
          class="text-lg md:text-xl font-semibold text-[var(--tactical-primary)] mb-2"
        >
          WebRTC Peer Connection
        </h2>
        <p
          class="text-sm md:text-base text-[var(--tactical-accent)] font-medium"
        >
          Secure SDP exchange protocol for tactical peer-to-peer communications
        </p>

        <!-- Technical Details -->
        <div class="flex items-center gap-4 mt-3">
          <div class="flex items-center space-x-2">
            <div
              class="w-2 h-2 bg-[var(--tactical-electric)] rounded-full"
            ></div>
            <span
              class="text-xs font-medium text-[var(--tactical-primary)] uppercase tracking-wide"
            >
              Real-time Protocol
            </span>
          </div>

          <div class="flex items-center space-x-2">
            <div class="w-2 h-2 bg-green-500 rounded-full"></div>
            <span
              class="text-xs font-medium text-[var(--tactical-primary)] uppercase tracking-wide"
            >
              Encrypted Channel
            </span>
          </div>

          <TooltipProvider>
            <Tooltip>
              <TooltipTrigger>
                <HelpCircle
                  class="h-4 w-4 text-[var(--tactical-electric)] hover:text-[var(--tactical-electric-light)] transition-colors"
                />
              </TooltipTrigger>
              <TooltipContent
                class="bg-[var(--tactical-primary)] text-white border-[var(--tactical-electric)]"
              >
                <p class="font-medium">Tactical Communication System</p>
                <p class="text-xs opacity-90">
                  Real-time status monitoring active
                </p>
              </TooltipContent>
            </Tooltip>
          </TooltipProvider>
        </div>
      </div>
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
