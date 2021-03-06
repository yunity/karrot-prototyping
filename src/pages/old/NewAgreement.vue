<template>
  <q-page class="flex flex-center">
    <div style="width: 800px;">
      <!--

        This is an implementation of https://community.foodsaving.world/t/stage-3-making-a-decision/608/4

      -->
      <q-card class="q-pa-md q-my-md">
        <q-card-section>
          <h3>Propose a new agreement</h3>
          <p>
            Here you can propose a new agreement for the group.
            It will be open for discussion and other editors will be able to collaborate with you on it.
            All members will be able to make comments and suggestions.
          </p>
          <p>
            When the time period is over, if enough people have voted positively for it, then it will have passed!
            Otherwise, you'll have an option to resubmit it with modifications.
          </p>
        </q-card-section>

        <q-card-section>
          <q-input
            label="Duration for discussion and voting"
            ref="dueBy"
            v-model="dueBy"
            outlined
            @blur="$refs.dueByProxy.hide()"
            @focus="$refs.dueByProxy.show()"
          >
            <component
              :is="smallScreen ? 'QDialog' : 'QMenu'"
              ref="dueByProxy"
              no-focus
              no-refocus
              no-parent-event
              @hide="$refs.dueBy.blur()"
            >
              <q-date
                v-model="dueBy"
                :mask="dueByMask"
                :navigation-min-year-month="'2021/03'"
                :options="isValidDate"
                minimal
                @input="() => smallScreen && $refs.dueByProxy.hide()"
              />
            </component>
            <template #append>
              {{ dueInWords }}
            </template>
          </q-input>
        </q-card-section>

        <q-card-section>
          <q-input
            v-model="agreement.title"
            label="Agreement Title"
            outlined
          />
        </q-card-section>

        <q-card-section>
          <q-input
            v-model="agreement.reason"
            label="Reason for proposal"
            outlined
            autogrow
            input-style="min-height: 100px;"
          />
        </q-card-section>

        <q-card-actions
          class="q-pa-sm"
          align="center"
        >
          <q-btn
            label="Start proposal process"
            color="primary"
            @click="submit"
          />
        </q-card-actions>
      </q-card>
    </div>
  </q-page>
</template>

<script>
import { date } from 'quasar'
import formatDistance from 'date-fns/formatDistance'

const { addToDate, formatDate, extractDate, adjustDate } = date

export default {
  data () {
    const dueByMask = 'YYYY-MM-DD'
    return {
      agreement: Object.assign(
        {},
        this.$root.$data.newAgreement,
        {
          dueBy: addToDate(new Date(), { days: 7 })
        }
      ), // would/should do deep clone here...
      dueByMask
    }
  },
  methods: {
    submit () {
      console.log('submitting', this.agreement)
      this.$root.$data.group.pendingAgreements.push(this.agreement)
      this.$router.push('/edit-agreement?id=' + (this.$root.$data.group.pendingAgreements.length - 1))
    },
    isValidDate (date) {
      // only allow future dates
      return date >= formatDate(new Date(), 'YYYY/MM/DD')
    }
  },
  computed: {
    smallScreen () {
      return this.$q.screen.width < 450 || this.$q.screen.height < 450
    },
    dueInWords () {
      return formatDistance(new Date(), this.agreement.dueBy)
    },
    dueBy: {
      get () {
        return formatDate(this.agreement.dueBy, this.dueByMask)
      },
      set (val) {
        if (!val) return // clicking on the selected date sets it to null, we don't want this behaviour
        val = extractDate(val, this.dueByMask)
        // would need to do a bit of timezone thinking...
        val = adjustDate(val, { hours: 23, minutes: 59, seconds: 59 })
        this.agreement.dueBy = val
      }
    }
  }
}
</script>
