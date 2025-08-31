<template>
  <Card class="tactical-card">
    <CardHeader
      class="bg-gradient-to-r from-[var(--tactical-primary)]/5 to-[var(--tactical-secondary)]/5"
    >
      <CardTitle class="flex items-center gap-3">
        <div class="tactical-gradient p-2 rounded-md">
          <Settings class="h-5 w-5 text-white" />
        </div>
        <div>
          <div
            class="text-[var(--tactical-primary)] font-bold tracking-wide text-lg"
          >
            TACTICAL CONTROLS
          </div>
          <div
            class="text-xs text-[var(--tactical-electric)] font-medium uppercase tracking-wider"
          >
            Communication Protocol
          </div>
        </div>
      </CardTitle>
      <CardDescription class="text-[var(--tactical-accent)] font-medium">
        Execute SDP exchange protocols for secure peer-to-peer communication
      </CardDescription>
    </CardHeader>
    <CardContent class="pt-6">
      <!-- Control Buttons Grid -->
      <div class="grid grid-cols-1 md:grid-cols-3 gap-4 mb-6">
        <!-- Create Offer Button -->
        <TooltipProvider>
          <Tooltip>
            <TooltipTrigger asChild>
              <Button
                @click="$emit('createOffer')"
                :disabled="isLoading"
                class="tactical-button h-14 bg-[var(--tactical-electric)] hover:bg-[var(--tactical-electric-light)] text-white font-bold tracking-wide transition-all duration-300 border-2 border-[var(--tactical-electric)] shadow-lg hover:shadow-xl"
                size="lg"
              >
                <div class="flex items-center space-x-2">
                  <Phone class="h-5 w-5" />
                  <div class="text-left">
                    <div class="text-sm">
                      {{
                        isLoading && loadingAction === "offer"
                          ? "INITIATING..."
                          : "CREATE OFFER"
                      }}
                    </div>
                    <div class="text-xs opacity-90 font-normal">
                      Start Connection
                    </div>
                  </div>
                </div>
              </Button>
            </TooltipTrigger>
            <TooltipContent
              class="bg-[var(--tactical-primary)] text-white border-[var(--tactical-electric)]"
            >
              <div class="font-medium">Initialize New Connection</div>
              <p class="text-xs opacity-90">
                Generate SDP offer for peer handshake
              </p>
            </TooltipContent>
          </Tooltip>
        </TooltipProvider>

        <!-- Create Answer Button -->
        <TooltipProvider>
          <Tooltip>
            <TooltipTrigger asChild>
              <Button
                @click="$emit('createAnswer')"
                :disabled="isLoading || !offerSdpValue"
                variant="secondary"
                class="tactical-button h-14 bg-[var(--tactical-secondary)] hover:bg-[var(--tactical-primary)] text-white font-bold tracking-wide transition-all duration-300 border-2 border-[var(--tactical-secondary)] shadow-lg hover:shadow-xl disabled:opacity-50"
                size="lg"
              >
                <div class="flex items-center space-x-2">
                  <PhoneCall class="h-5 w-5" />
                  <div class="text-left">
                    <div class="text-sm">
                      {{
                        isLoading && loadingAction === "answer"
                          ? "PROCESSING..."
                          : "CREATE ANSWER"
                      }}
                    </div>
                    <div class="text-xs opacity-90 font-normal">
                      Respond to Offer
                    </div>
                  </div>
                </div>
              </Button>
            </TooltipTrigger>
            <TooltipContent
              class="bg-[var(--tactical-primary)] text-white border-[var(--tactical-electric)]"
            >
              <div class="font-medium">Respond to Incoming Connection</div>
              <p class="text-xs opacity-90">
                Generate SDP answer for peer handshake
              </p>
            </TooltipContent>
          </Tooltip>
        </TooltipProvider>

        <!-- Add Answer Button -->
        <TooltipProvider>
          <Tooltip>
            <TooltipTrigger asChild>
              <Button
                @click="$emit('addAnswer')"
                :disabled="isLoading || !answerSdpValue"
                class="tactical-button h-14 bg-[var(--tactical-accent)] hover:bg-[var(--tactical-tertiary)] text-white font-bold tracking-wide transition-all duration-300 border-2 border-[var(--tactical-accent)] shadow-lg hover:shadow-xl disabled:opacity-50"
                size="lg"
              >
                <div class="flex items-center space-x-2">
                  <Check class="h-5 w-5" />
                  <div class="text-left">
                    <div class="text-sm">
                      {{
                        isLoading && loadingAction === "add"
                          ? "ESTABLISHING..."
                          : "ADD ANSWER"
                      }}
                    </div>
                    <div class="text-xs opacity-90 font-normal">
                      Complete Handshake
                    </div>
                  </div>
                </div>
              </Button>
            </TooltipTrigger>
            <TooltipContent
              class="bg-[var(--tactical-primary)] text-white border-[var(--tactical-electric)]"
            >
              <div class="font-medium">Finalize Connection</div>
              <p class="text-xs opacity-90">
                Complete peer-to-peer handshake protocol
              </p>
            </TooltipContent>
          </Tooltip>
        </TooltipProvider>
      </div>

      <!-- Protocol Status Indicators -->
      <div
        class="bg-[var(--tactical-primary)]/5 rounded-lg p-4 border border-[var(--tactical-primary)]/20"
      >
        <div
          class="text-sm font-bold text-[var(--tactical-primary)] mb-3 uppercase tracking-wide"
        >
          Protocol Status
        </div>
        <div class="grid grid-cols-1 md:grid-cols-3 gap-4 text-xs">
          <div class="flex items-center space-x-2">
            <div
              class="w-3 h-3 rounded-full"
              :class="offerSdpValue ? 'bg-green-500' : 'bg-gray-400'"
            ></div>
            <span class="font-medium text-[var(--tactical-primary)]"
              >OFFER SDP</span
            >
            <span class="text-[var(--tactical-accent)]">{{
              offerSdpValue ? "READY" : "PENDING"
            }}</span>
          </div>
          <div class="flex items-center space-x-2">
            <div
              class="w-3 h-3 rounded-full"
              :class="answerSdpValue ? 'bg-green-500' : 'bg-gray-400'"
            ></div>
            <span class="font-medium text-[var(--tactical-primary)]"
              >ANSWER SDP</span
            >
            <span class="text-[var(--tactical-accent)]">{{
              answerSdpValue ? "READY" : "PENDING"
            }}</span>
          </div>
          <div class="flex items-center space-x-2">
            <div
              class="w-3 h-3 rounded-full"
              :class="isLoading ? 'bg-yellow-500 animate-pulse' : 'bg-gray-400'"
            ></div>
            <span class="font-medium text-[var(--tactical-primary)]"
              >PROCESSING</span
            >
            <span class="text-[var(--tactical-accent)]">{{
              isLoading ? "ACTIVE" : "IDLE"
            }}</span>
          </div>
        </div>
      </div>
    </CardContent>
  </Card>
</template>

<script setup lang="ts">
import { Settings, Phone, PhoneCall, Check } from "lucide-vue-next";

defineEmits<{
  (e: "createOffer"): void;
  (e: "createAnswer"): void;
  (e: "addAnswer"): void;
}>();

defineProps<{
  answerSdpValue: string;
  offerSdpValue: string;
  isLoading: boolean;
  loadingAction: "offer" | "answer" | "add" | null;
}>();
</script>
