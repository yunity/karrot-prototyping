<template>
  <q-page class="flex flex-center">
    <div style="width: 800px;">
      <q-stepper
        v-model="step"
        ref="stepper"
        color="primary"
        header-nav
      >
        <q-step
          :name="1"
          title="Vision"
          caption="Mandatory"
          icon="emoji_objects"
          :done="Boolean(vision)"
        >
          <p class="text-h5">What is your group's vision?</p>
          <q-input
            v-model="vision"
            autogrow
            filled
            placeholder="What do you want to achieve? This information will also be made public."
            hint="Markdown ..."
          />
        </q-step>

        <q-step
          :name="2"
          title="Decision-making"
          caption="Optional"
          icon="thumbs_up_down"
          :done="Boolean(governance)"
        >
          <p class="text-h5">How does your group make decisions?</p>
          <q-input
            v-model="governance"
            filled
            autogrow
            placeholder="In other words, what is the governance structure of your group?"
            hint="Markdown ..."
          />
        </q-step>

        <q-step
          :name="3"
          title="General Agreement"
          caption="Optional"
          icon="fas fa-handshake"
          :done="Boolean(agreement)"
        >
          <p class="text-h5">You can propose an agreement to all future members of your group.</p>
          <p>Your agreement proposal will be pending until other two trusted members approve it.</p>
          <q-input
            v-model="agreement"
            filled
            autogrow
            placeholder="What should everybody agree to in order to join this group?"
            hint="Markdown ...."
          />
        </q-step>

        <template v-slot:navigation>
          <q-stepper-navigation>
            <q-btn @click="$refs.stepper.next()" color="primary" :label="step === 3 ? 'Finish' : 'Continue'" :disable="vision === ''"/>
            <q-btn v-if="step > 1" flat color="primary" @click="$refs.stepper.previous()" label="Back" class="q-ml-sm" />
          </q-stepper-navigation>
        </template>
      </q-stepper>
    </div>
  </q-page>
</template>

<script>
export default {
  name: 'PageIndex',
  data () {
    return {
      step: 1,
      vision: '',
      governance: '',
      agreement: ''
    }
  }
}
</script>
