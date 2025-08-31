<template>
  <Card>
    <CardHeader>
      <CardTitle class="flex items-center gap-2">
        <Settings class="h-5 w-5" />
        Connection Controls
      </CardTitle>
      <CardDescription>
        Create and exchange SDP offers and answers to establish peer connection
      </CardDescription>
    </CardHeader>
    <CardContent>
      <div class="grid grid-cols-1 md:grid-cols-3 gap-4">
        <TooltipProvider>
          <Tooltip>
            <TooltipTrigger asChild>
              <Button
                @click="$emit('createOffer')"
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
                @click="$emit('createAnswer')"
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
                @click="$emit('addAnswer')"
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
