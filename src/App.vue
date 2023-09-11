<script setup>
import { ref, onMounted } from "vue";
const transcript = ref("");
const isRecording = ref(false);

const Recognition = window.SpeechRecognition || window.webkitSpeechRecognition;
const sr = new Recognition();

onMounted(() => {
  sr.continuous = true;
  sr.interimResults = true;

  sr.onstart = () => {
    console.log("SR Started");
    isRecording.value = true;
  };

  sr.onend = () => {
    console.log("SR Stopped");
    isRecording.value = false;
  };

  sr.onresult = (e) => {
    for (let i = 0; i < e.results.length; i++) {
      const result = e.results[i];
      if (result.isFinal) CheckForCommand(result);
    }

    const t = Array.from(e.results)
      .map((result) => result[0])
      .map((result) => result.transcript)
      .join("");

    transcript.value = t;
  };
});

const CheckForCommand = (result) => {
  const t = result[0].transcript;
  if (t.includes("stop recording")) {
    sr.stop();
  } else if (t.includes("what is the time") || t.includes("what's the time")) {
    sr.stop();
    alert(new Date().toLocaleTimeString());
    setTimeout(() => sr.start(), 100);
  }
};

const ToggleMic = () => {
  if (isRecording.value) {
    sr.stop();
  } else {
    sr.start();
  }
};
</script>

<template>
  <div class="app">
    <button :class="`mic`" @click="ToggleMic">Record</button>
    <div class="transcript" v-text="transcript"></div>
  </div>
</template>
