<template>
  <v-layout column justify-center align-end pa-5>
    <v-textarea
      outlined
      :rules="[required]"
      label="Input"
      placeholder="Write/Paste your (HTML) input here..."
      class="w100p"
      @input="handleInput"
    />
    <div class="d-flex flex w100p justify-space-between mb-3">
      <p>Output:</p>
      <v-btn color="primary" @click="copy">
        <v-icon dark>mdi-content-copy</v-icon>
        <span class="ml-2">{{ copyButtonText }}</span>
      </v-btn>
    </div>
    <output
      ref="output"
      class="output mb-3 pa-3"
      readonly
      title="Output"
      :class="{
        empty: !output,
      }"
      v-text="output"
    ></output>
  </v-layout>
</template>

<script>
import { cleanHTML } from '@graficos/html-cleanser'
import SimpleDebounce from 'simple-debounce-vue-mixin'
import { copyToClipBoard } from 'copytoclipboard'

const copyButtonText = 'Copy to Clipboard'

export default {
  mixins: [SimpleDebounce],
  data() {
    return {
      output: '',
      copyButtonText,
    }
  },
  methods: {
    handleInput(src) {
      this.simpleDebounce(() => {
        this.processText(src)
      })
    },
    processText(src) {
      const cleanString = cleanHTML(src)
      this.output = cleanString
      // show done + copy icon + hide done
    },
    copy() {
      const element = this.$refs.output
      copyToClipBoard(element)
        .then(() => {
          // eslint-disable-next-line no-console
          console.log('✅ it worked ! ("Ctrl/Cmd + V" to paste it)')
          this.copyButtonText = 'Copied!'
          setTimeout(() => {
            this.copyButtonText = copyButtonText
          }, 3000)
        })
        .catch((e) => {
          // eslint-disable-next-line no-console
          console.log('❌ something went wrong', e)
          this.copyButtonText = 'something went wrong :('
        })
    },
    required(value) {
      return !!value || 'Insert your input here'
    },
  },
}
</script>

<style scoped>
.w100p {
  width: 100%;
}
@media (min-width: 320px) {
  .output {
    padding-left: 8px;
  }
}
.output {
  width: 100%;
  border: 1px solid #777;
  border-radius: 4px;
  min-height: 150px;
  white-space: pre-wrap;
}
.output.empty {
  border: 1px solid #7777778f;
}
</style>
