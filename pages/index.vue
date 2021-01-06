<template>
  <v-container>
    <v-row justify="center" align="center" class="flex-column">
      <v-col>
        <v-row justify="center" align="center" class="flex-column">
          <div class="text-h5">FingerprintJS Free</div>
          <span class="text-h6">
            <b>{{ visitorId }}</b>
          </span>
        </v-row>
      </v-col>
      <v-spacer class="mt-4"></v-spacer>
      <v-col>
        <v-row justify="center" align="center" class="flex-column">
          <div class="text-h5">Canvas FingerPrinting</div>
          <span class="text-h6">
            <b>{{ canvasId }}</b>
          </span>
        </v-row>
      </v-col>
      <v-spacer class="mt-4"></v-spacer>
      <v-col>
        <v-row justify="center" align="center" class="flex-column">
          <div class="text-h5">Audio DeviceId</div>
          <span
            class="text-h6"
            v-for="(item, index) in audioDevices"
            :key="index"
          >
            <b>{{ item.deviceId || "" }}</b>
          </span>
        </v-row>
      </v-col>
    </v-row>
  </v-container>
</template>

<script lang="ts">
import { Component, Vue, Watch } from "nuxt-property-decorator";
import FingerprintJS from "@fingerprintjs/fingerprintjs";

@Component({})
export default class HomePage extends Vue {
  visitorId: string = "";
  canvasId: string = "";
  audioDevices: MediaDeviceInfo[] = [];

  async mounted(): Promise<void> {
    // FINGERPRINTJS FREE METHOD
    const fp = await FingerprintJS.load();
    const result = await fp.get();
    if (result) {
      this.visitorId = result.visitorId;
    }

    // CANVAS FINGERPRINT METHOD
    this.canvasId = this.canvasFingerprint();

    // METHOD AUDIO DEVICE ID
    const getUserMedia = await navigator.mediaDevices.getUserMedia({
      audio: true
    });
    const devices = await navigator.mediaDevices.enumerateDevices();
    this.audioDevices = devices.filter(item => {
      return !["", "default"].includes(item.deviceId);
    });
  }

  canvasFingerprint(): any {
    const canvas = document.createElement("canvas");
    const ctx = canvas.getContext("2d");
    const txt = "i9asdm..$#po((^@KbXrww!~cz";

    if (ctx) {
      ctx.textBaseline = "top";
      ctx.font = "14px 'Arial'";
      ctx.textBaseline = "alphabetic";
      ctx.rotate(0.05);
      ctx.fillStyle = "#f60";
      ctx.fillRect(125, 1, 62, 20);
      ctx.fillStyle = "#069";
      ctx.fillText(txt, 2, 15);
      ctx.fillStyle = "rgba(111, 111, 0, 0.7)";
      ctx.fillText(txt, 4, 17);
      ctx.shadowBlur = 5;
      ctx.shadowColor = "purple";
      ctx.fillRect(-20, 10, 234, 5);
      const strng = canvas.toDataURL();

      let char;
      let hash = 0;
      if (strng.length == 0) return "";
      for (let i = 0; i < strng.length; i++) {
        char = strng.charCodeAt(i);
        hash = (hash << 10) - hash + char;
        hash = hash & hash;
      }
      return Math.abs(hash)?.toString() || "";
    }
  }
}
</script>
