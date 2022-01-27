<template>
  <b-container>
    <b-row class="justify-content-md-center">
      <b-col align="center" cols="12">
        <select v-model="camera">
          <option :value="null">-- {{$t('selectCamDevice')}} --</option>
          <option v-for="device in devices"
                  :key="device.deviceId"
                  :value="device.deviceId">{{ device.label }}
          </option>
        </select></b-col>
    </b-row>
    <b-row class="justify-content-md-center">
      <b-col align="center" cols="12">
        <code v-if="device">{{ device.label }}</code>
      </b-col>
    </b-row>
    <b-row>
      <b-col align="center" cols="12">
        <web-cam ref="webcam"
                 :device-id="deviceId"
                 width="70%"
                 height="100%"
                 @error="onError"
                 @cameras="onCameras"
                 @camera-change="onCameraChange"/>
      </b-col>
    </b-row>
    <b-row class="mt-2 justify-content-md-center">
      <b-col align="center" cols="12">
        <b-button
          pill
          :title="$t('captureImage')"
          :block="isSmallScreen"
          variant="outline-primary"
          class="mt-2 mb-2"
          :disabled="!camera"
          @click="onCapture">
            <span class="fa-stack">
        <i class="fa fa-camera-retro fa-stack-1x"></i>
        <i class="fa fa-stack-2x text-success"></i>
          </span>
          {{ $t('captureImage') }}
        </b-button>
      </b-col>
    </b-row>
    <b-row class="mb-2 mt-2 justify-content-md-center">
      <b-col align="center" cols="12">
        <b-button
          pill
          :title="$t('closeCamera')"
          :block="isSmallScreen"
          variant="outline-primary"
          class="mb-2"
          @click="onStop">
          <span class="fa-stack">
        <i class="fa fa-camera fa-stack-1x"></i>
        <i class="fa fa-ban fa-stack-2x text-danger"></i>
          </span>
          {{ $t('closeCamera') }}
        </b-button>
      </b-col>
    </b-row>

  </b-container>
</template>

<script>
  import {WebCam} from 'vue-web-cam';
  import {find, head} from 'lodash';

  export default {
    components: {
      WebCam
    },
    data() {
      return {
        img: null,
        camera: null,
        deviceId: null,
        devices: []
      };
    },
    computed: {
      isSmallScreen() {
        return this.$mq === 'xs' || this.$mq === 'sm';
      },
      device() {
        return find(this.devices, n => n.deviceId == this.deviceId);
      }
    },
    watch: {
      camera: function (id) {
        this.deviceId = id;
      },
      devices: function () {
        // Once we have a list select the first one
        let first = head(this.devices);
        if (first) {
          this.camera = first.deviceId;
          this.deviceId = first.deviceId;
        }
      }
    },
    methods: {
      onCapture() {
        this.img = this.$refs.webcam.capture();
        this.$emit('on-camera-capture', this.img);
      },
      onStop() {
        this.$refs.webcam.stop();
        this.$emit('on-camera-close');
      },
      onStart() {
        this.$refs.webcam.start();
      },
      onError(error) {
      },
      onCameras(cameras) {
        this.devices = cameras;
      },
      onCameraChange(deviceId) {
        this.deviceId = deviceId;
        this.camera = deviceId;
      }
    },
    beforeDestroy() {
      this.$refs.webcam.stop();
    }
  };
</script>
