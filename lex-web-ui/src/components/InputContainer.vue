<template>
  <div app fixed class="input-wrapper">
    <v-layout
      ma-0
      class="input-container"
    >
      <v-toolbar
        v-if="!hidden"
        color="white"
        v-bind:dense="this.$store.state.isRunningEmbedded"
      >
        <!--
          using v-show instead of v-if to make recorder-status transition work
        -->
        <v-text-field
          v-bind:label="disabled ? textInputPlaceholderDisabled : textInputPlaceholder"
          v-show="shouldShowTextInput"
          v-bind:disabled="isLexProcessing || disabled || loading"
          v-model="textInput"
          v-on:keyup.enter.stop="postTextMessage"
          v-on:focus="onTextFieldFocus"
          v-on:blur="onTextFieldBlur"
          ref="textInput"
          id="text-input"
          name="text-input"
          single-line
          hide-details
        ></v-text-field>

        <recorder-status
          v-show="!shouldShowTextInput"
        ></recorder-status>

        <!-- separate tooltip as a workaround to support mobile touch events -->
        <!-- tooltip should be before btn to avoid right margin issue in mobile -->
        <v-tooltip
          activator=".input-button"
          content-class="tooltip-custom"
          v-model="shouldShowTooltip"
          ref="tooltip"
          left
        >
          <span id="input-button-tooltip">{{inputButtonTooltip}}</span>
        </v-tooltip>
        <v-btn
          v-if="shouldShowSendButton"
          v-on:click="postTextMessage"
          v-on="tooltipEventHandlers"
          v-bind:disabled="isLexProcessing"
          ref="send"
          class="icon-color input-button"
          icon
          aria-label="Send Message"
        >
          <v-icon medium>send</v-icon>
        </v-btn>
        <v-btn
          v-else
          v-on:click="onMicClick"
          v-on="tooltipEventHandlers"
          v-bind:disabled="isMicButtonDisabled
          || isLexProcessing || disabled || loading"
          ref="mic"
          class="icon-color input-button"
          icon
        >
          <v-icon medium>{{micButtonIcon}}</v-icon>
        </v-btn>
      </v-toolbar>
      <div class="branding">
        Powered by
        <a href="https://getciville.com/" target="_blank" style="font-size: 0;">
        <svg width="46" height="25" viewBox="0 0 41 22" fill="none" xmlns="http://www.w3.org/2000/svg">
          <g clip-path="url(#clip0)">
          <path d="M0 3.62V22L32.6281 19.1167V22L41 18.38V0L0 3.62Z" fill="#C3C3C3"/>
          <path d="M32.6289 19.1167V22L41.0008 18.38L32.6289 19.1167Z" fill="#B0B0B0"/>
          <!-- eslint-disable-next-line -->
          <path d="M8.73336 13.4V14.8233C8.73336 16.5367 7.88694 17.59 6.2534 17.7367C4.61985 17.88 3.77344 16.9767 3.77344 15.2633V9.69334C3.77344 7.98001 4.61985 6.92667 6.2534 6.78001C7.88694 6.63667 8.73336 7.54001 8.73336 9.25334V10.2933L7.16238 10.4333V9.28667C7.16238 8.52001 6.82975 8.26001 6.2995 8.30667C5.76926 8.35334 5.43663 8.67334 5.43663 9.44001V15.2233C5.43663 15.99 5.76926 16.2333 6.2995 16.1867C6.82975 16.14 7.16238 15.8367 7.16238 15.07V13.54L8.73336 13.4ZM15.4355 14.87L16.7068 6.02334L18.2349 5.88667L16.6014 16.74L14.1214 16.96L12.4879 6.39667L14.1675 6.24667L15.4355 14.87ZM9.75762 6.66334L11.4208 6.51667V17.2267L9.75762 17.3733V6.66334ZM22.4209 5.48001L24.0841 5.33334V14.5133L26.8209 14.27V15.8L22.4209 16.19V5.48001ZM27.5554 5.02667L29.2185 4.88001V14.06L31.9554 13.8167V15.3467L27.5554 15.7367V5.02667ZM34.3497 8.94001L36.6321 8.73667V10.2667L34.3497 10.47V13.6067L37.2216 13.3533V14.8833L32.6865 15.2867V4.57334L37.2216 4.17334V5.70334L34.3497 5.95667V8.94001ZM20.7808 9.23334V8.94334C20.5436 8.88667 20.2966 8.86001 20.0496 8.86001C19.7993 8.86001 19.5556 8.88667 19.3185 8.94334V9.23334C19.5576 9.1731 19.8032 9.14287 20.0496 9.14334C20.296 9.14307 20.5416 9.17329 20.7808 9.23334ZM20.0496 10.95C19.7598 10.95 19.4766 11.0067 19.2131 11.12C19.0924 11.1727 18.9767 11.2363 18.8673 11.31V11.66C19.0064 11.5458 19.1605 11.4516 19.3251 11.38C19.5556 11.2833 19.7993 11.2333 20.0529 11.2333C20.3065 11.2333 20.5502 11.2833 20.7808 11.38C20.9454 11.45 21.1002 11.5433 21.2385 11.66V11.31C21.1292 11.2363 21.0134 11.1727 20.8927 11.12C20.6255 11.0078 20.339 10.95 20.0496 10.95ZM20.0496 11.5167C19.838 11.5165 19.6285 11.5596 19.4337 11.6433L19.2658 11.7267L19.414 15.2L19.4239 15.4133L19.4272 15.5L19.4436 15.8667L19.4502 16.0533C19.4588 16.0954 19.4709 16.1367 19.4864 16.1767C19.5178 16.251 19.5625 16.3188 19.6182 16.3767C19.6742 16.4333 19.74 16.48 19.8125 16.51C19.8883 16.5435 19.9702 16.5605 20.0529 16.56C20.1352 16.56 20.2176 16.5433 20.2933 16.51C20.3658 16.48 20.4317 16.4333 20.4876 16.3767C20.5436 16.32 20.5897 16.2533 20.6194 16.1767C20.6358 16.1367 20.649 16.0967 20.6556 16.0533L20.6622 15.8667L20.6787 15.5L20.682 15.4133L20.6918 15.2L20.705 14.8967L20.7083 14.8267L20.7182 14.5833L20.7215 14.53L20.84 11.7267L20.6721 11.6433C20.4712 11.56 20.2637 11.5167 20.0496 11.5167ZM21.7161 10.9633C21.693 10.9433 21.6634 10.9333 21.6371 10.9333V10.26C21.6766 10.26 21.7161 10.24 21.7424 10.2067C21.7645 10.1767 21.7745 10.1392 21.7702 10.102C21.7659 10.0649 21.7477 10.0307 21.7194 10.0067C21.4416 9.78635 21.1238 9.62328 20.784 9.52667C20.546 9.45944 20.3 9.42579 20.0529 9.42667C19.8059 9.42721 19.56 9.46084 19.3218 9.52667C18.976 9.62334 18.6598 9.79001 18.3864 10.0067C18.3717 10.0181 18.3593 10.0324 18.3502 10.0487C18.341 10.065 18.3351 10.0831 18.333 10.1017C18.3308 10.1204 18.3324 10.1393 18.3376 10.1573C18.3429 10.1754 18.3516 10.1921 18.3634 10.2067C18.3897 10.24 18.4292 10.26 18.4688 10.26V10.9333C18.4424 10.9333 18.4128 10.9433 18.3897 10.9633C18.3271 11.01 18.3173 11.1 18.3667 11.1633C18.4128 11.2233 18.4951 11.2367 18.5577 11.1933C18.561 11.1933 18.561 11.19 18.5643 11.19C18.5676 11.19 18.5676 11.1867 18.5709 11.1833C18.7388 11.0533 18.92 10.9433 19.1143 10.86C19.414 10.7333 19.7301 10.6667 20.0595 10.6667C20.3888 10.6667 20.705 10.73 21.0047 10.86C21.1992 10.9429 21.382 11.0516 21.5481 11.1833C21.5514 11.1833 21.5514 11.1867 21.5547 11.19C21.558 11.1933 21.558 11.1933 21.5613 11.1933C21.6206 11.2367 21.7062 11.2233 21.7523 11.1633C21.7885 11.1 21.7754 11.01 21.7161 10.9633ZM21.4131 6.46001C21.3323 6.43979 21.2493 6.42971 21.1661 6.43001C21.0607 6.43001 20.9553 6.44667 20.8532 6.47667C20.8796 6.35402 20.8928 6.22886 20.8927 6.10334C20.8927 6.04209 20.8894 5.98089 20.8829 5.92001C20.8664 5.76001 20.8302 5.60334 20.7709 5.45667C20.7808 5.56001 20.7511 5.70001 20.6885 5.85334C20.6622 5.92334 20.626 5.99334 20.5831 6.06667C20.4778 6.25001 20.3526 6.39667 20.2406 6.48001C20.1418 6.55334 20.0529 6.58001 19.9936 6.54334C19.9673 6.52667 19.9475 6.50334 19.9376 6.46667C19.8981 6.35001 19.9376 6.14001 20.043 5.91001C19.9218 5.96195 19.8121 6.03788 19.7203 6.13334C19.6237 6.22889 19.5474 6.34347 19.4963 6.47001C19.4403 6.61334 19.3942 6.68001 19.3119 6.77001C19.2197 6.87001 19.1406 6.98001 19.0879 7.10667C19.0352 7.23667 19.0056 7.37334 19.0056 7.51667C19.0059 7.62392 19.0214 7.73056 19.0517 7.83334C19.0616 7.86667 19.0715 7.89667 19.0846 7.92667C19.1373 8.05334 19.2131 8.16334 19.3086 8.26334C19.4041 8.36001 19.5161 8.43667 19.6412 8.49001C19.7697 8.54334 19.9047 8.57334 20.0463 8.57334C20.1856 8.57438 20.3236 8.546 20.4514 8.49001C20.514 8.46334 20.5733 8.43001 20.6293 8.39001C20.682 8.35334 20.7346 8.31001 20.7808 8.26334C20.8763 8.16667 20.952 8.05334 21.0047 7.92667C21.0304 7.8654 21.0503 7.80177 21.064 7.73667C21.0772 7.66667 21.087 7.59334 21.087 7.51667C21.087 7.46667 21.0838 7.42001 21.0772 7.37001L21.0739 7.32334C21.0706 7.29667 21.0706 7.26667 21.0706 7.24001C21.0695 7.09903 21.0976 6.95939 21.1529 6.83001C21.2056 6.70334 21.2814 6.59001 21.3769 6.49334L21.4098 6.46001H21.4131ZM20.3032 7.87001C20.2536 7.95679 20.1721 8.0202 20.0765 8.04642C19.9808 8.07264 19.8789 8.05956 19.7927 8.01001C19.7502 7.98517 19.713 7.95208 19.6832 7.91262C19.6534 7.87317 19.6315 7.82813 19.619 7.78011C19.6064 7.73209 19.6034 7.68203 19.61 7.63281C19.6167 7.5836 19.6329 7.5362 19.6577 7.49334C19.7598 7.31334 20.3559 7.02667 20.3559 7.02667C20.3559 7.02667 20.4086 7.69001 20.3032 7.87001Z" fill="white"/>
          </g>
          <defs>
          <clipPath id="clip0">
          <rect width="41" height="22" fill="white"/>
          </clipPath>
          </defs>
        </svg>
        </a>
      </div>
    </v-layout>
  </div>
</template>

<script>
/*
Copyright 2017-2019 Amazon.com, Inc. or its affiliates. All Rights Reserved.

Licensed under the Amazon Software License (the "License"). You may not use this file
except in compliance with the License. A copy of the License is located at

http://aws.amazon.com/asl/

or in the "license" file accompanying this file. This file is distributed on an "AS IS"
BASIS, WITHOUT WARRANTIES OR CONDITIONS OF ANY KIND, express or implied. See the
License for the specific language governing permissions and limitations under the License.
*/
/* eslint no-console: ["error", { allow: ["warn", "error"] }] */

import RecorderStatus from '@/components/RecorderStatus';

export default {
  name: 'input-container',
  data() {
    return {
      textInput: '',
      isTextFieldFocused: false,
      shouldShowTooltip: false,
      // workaround: vuetify tooltips doesn't seem to support touch events
      tooltipEventHandlers: {
        mouseenter: this.onInputButtonHoverEnter,
        mouseleave: this.onInputButtonHoverLeave,
        touchstart: this.onInputButtonHoverEnter,
        touchend: this.onInputButtonHoverLeave,
        touchcancel: this.onInputButtonHoverLeave,
      },
    };
  },
  props: ['textInputPlaceholder', 'initialSpeechInstruction', 'hasButtons'],
  components: {
    RecorderStatus,
  },
  computed: {
    disabled() {
      return this.$store.state.config.ui.disableInputFieldsForButtonResponse && this.hasButtons;
    },
    hidden() {
      return this.$store.state.config.ui.hideInputFieldsForButtonResponse && this.hasButtons;
    },
    textInputPlaceholderDisabled() {
      return this.$store.state.config.ui.textInputPlaceholderDisabled || this.textInputPlaceholder;
    },
    isBotSpeaking() {
      return this.$store.state.botAudio.isSpeaking;
    },
    isLexProcessing() {
      return this.$store.state.lex.isProcessing;
    },
    isSpeechConversationGoing() {
      return this.$store.state.recState.isConversationGoing;
    },
    isMicButtonDisabled() {
      return this.isMicMuted;
    },
    isMicMuted() {
      return this.$store.state.recState.isMicMuted;
    },
    isRecorderSupported() {
      return this.$store.state.recState.isRecorderSupported;
    },
    isRecorderEnabled() {
      return this.$store.state.recState.isRecorderEnabled;
    },
    isSendButtonDisabled() {
      return this.textInput.length < 1;
    },
    micButtonIcon() {
      if (this.isMicMuted) {
        return 'mic_off';
      }
      if (this.isBotSpeaking || this.isSpeechConversationGoing) {
        return 'stop';
      }
      return 'mic';
    },
    inputButtonTooltip() {
      if (this.shouldShowSendButton) {
        return 'send';
      }
      if (this.isMicMuted) {
        return 'mic seems to be muted';
      }
      if (this.isBotSpeaking || this.isSpeechConversationGoing) {
        return 'interrupt';
      }
      return 'click to use voice';
    },
    shouldShowSendButton() {
      return (
        (this.textInput.length && this.isTextFieldFocused)
        || (!this.isRecorderSupported || !this.isRecorderEnabled)
      );
    },
    shouldShowTextInput() {
      return !(this.isBotSpeaking || this.isSpeechConversationGoing);
    },
    loading() {
      return this.$store.state.lex.isProcessing;
    },
  },
  methods: {
    onInputButtonHoverEnter() {
      this.shouldShowTooltip = true;
    },
    onInputButtonHoverLeave() {
      this.shouldShowTooltip = false;
    },
    onMicClick() {
      this.onInputButtonHoverLeave();
      if (this.isBotSpeaking || this.isSpeechConversationGoing) {
        return this.$store.dispatch('interruptSpeechConversation');
      }
      if (!this.isSpeechConversationGoing) {
        this.processAnalytics();

        return this.startSpeechConversation();
      }

      return Promise.resolve();
    },
    onTextFieldFocus() {
      this.isTextFieldFocused = true;
    },
    onTextFieldBlur() {
      if (!this.textInput.length && this.isTextFieldFocused) {
        this.isTextFieldFocused = false;
      }
    },
    setInputTextFieldFocus() {
      // focus() needs to be wrapped in setTimeout for IE11
      setTimeout(() => {
        if (this.$refs && this.$refs.textInput && this.shouldShowTextInput) {
          this.$refs.textInput.$refs.input.focus();
        }
      }, 10);
    },
    playInitialInstruction() {
      const isInitialState = ['', 'Fulfilled', 'Failed']
        .some(initialState => (
          this.$store.state.lex.dialogState === initialState
        ));

      return (this.$store.state.isLoggedIn && isInitialState
        && this.initialSpeechInstruction.length > 0)
        ? this.$store.dispatch(
          'pollySynthesizeSpeech',
          this.initialSpeechInstruction,
        )
        : Promise.resolve();
    },
    processAnalytics() {
      if (!this.$store.state.isInterationStarted) {
        this.$store.dispatch(
          'sendMessageToParentWindow',
          { event: 'firstInteraction' },
        );
        this.$store.commit('setInteractionStarted');
      }
    },
    postTextMessage() {
      this.onInputButtonHoverLeave();
      this.textInput = this.textInput.trim();
      // empty string
      if (!this.textInput.length) {
        return Promise.resolve();
      }

      this.processAnalytics();

      const message = {
        type: 'human',
        text: this.textInput,
      };

      return this.$store.dispatch('postTextMessage', message)
        .then(() => {
          this.textInput = '';
          if (this.shouldShowTextInput) {
            this.setInputTextFieldFocus();
          }
        });
    },
    startSpeechConversation() {
      if (this.isMicMuted) {
        return Promise.resolve();
      }
      return this.setAutoPlay()
        .then(() => this.playInitialInstruction())
        .then(() => this.$store.dispatch('startConversation'))
        .catch(error => {
          console.error('error in startSpeechConversation', error);
          const errorMessage = (this.$store.state.config.ui.showErrorDetails)
            ? ` ${error}` : '';

          this.$store.dispatch(
            'pushErrorMessage',
            "Sorry, I couldn't start the conversation. Please try again."
            + `${errorMessage}`,
          );
        });
    },
    /**
     * Set auto-play attribute on audio element
     * On mobile, Audio nodes do not autoplay without user interaction.
     * To workaround that requirement, this plays a short silent audio mp3/ogg
     * as a reponse to a click. This silent audio is initialized as the src
     * of the audio node. Subsequent play on the same audio now
     * don't require interaction so this is only done once.
     */
    setAutoPlay() {
      if (this.$store.state.botAudio.autoPlay) {
        return Promise.resolve();
      }
      return this.$store.dispatch('setAudioAutoPlay');
    },
  },
};
</script>
<style>
.input-container .input-group label {
    color: rgba(0,0,0,.6) !important;
}
.input-container .input-group--disabled label {
    color: rgba(0,0,0,.3) !important;
}

.input-container .btn.input-button {
  color: #000 !important;
  opacity: 0.5;
}

.input-wrapper {
  margin-bottom: 25px;
  padding-right: 20px;
  padding-left: 20px;
}
.input-container {
  /* make footer same height as dense toolbar */
  min-height: 48px;
  align-items: center;
  justify-content: center;
  flex-direction: column;
}

.input-container .toolbar {
  border: 1px solid #E9E9E9 !important;
  border-radius: 8px;
  box-shadow: none;
}

.input-container .input-group__details {
  display: none;
}

.input-container .btn {
  color: #BDC8D1 !important;
}

.input-container .branding {
  margin-top: 20px;
  display: flex;
  align-items: center;
  color: #B0B0B0;
}

.input-container .branding svg {
  margin-left: 5px;
}
</style>
