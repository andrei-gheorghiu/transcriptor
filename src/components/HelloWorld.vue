<template>
  <div class="hello">
    {{ transcription }}
  </div>
</template>

<script lang="ts">
import { defineComponent } from "vue";

// Creates a client
type Alternative = {
  transcript: string;
};
type Result = {
  alternatives: Alternative[];
};
type Response = {
  results: Result[];
};

export default defineComponent({
  name: "HelloWorld",
  data: () => ({
    transcription: "",
    client: null
  }),
  async mounted() {
    const speech = await require("@google-cloud/speech");
    this.client = new speech.SpeechClient();
  },
  methods: {
    quickstart: async function() {
      const [response] = await this.client?.recognize({
        audio: {
          uri: "gs://cloud-samples-data/speech/brooklyn_bridge.raw"
        },
        config: {
          encoding: "LINEAR16",
          sampleRateHertz: 16000,
          languageCode: "en-US"
        }
      });
      this.transcription = (response as Response).results
        .map(result => result.alternatives[0].transcript)
        .join("\n");
    }
  }
});
</script>
