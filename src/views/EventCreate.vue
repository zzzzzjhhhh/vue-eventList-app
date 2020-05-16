<template>
  <div>
    <h1>Create an event</h1>
    <form @submit.prevent="createEvent">
      <BaseSelect
        label="Select a category"
        v-model="event.category"
        :options="categories"
        :class="{ error: $v.event.category.$error }"
        @blur="$v.event.category.$touch()"
      />
      <template v-if="$v.event.category.$error">
        <p class="errorMessage" v-if="!$v.event.category.required">
          Category is required.
        </p>
      </template>

      <h3>Name & describe your event</h3>
      <BaseInput
        class="field"
        label="Title"
        v-model="event.title"
        type="text"
        placeholder="Title"
        :class="{ error: $v.event.title.$error }"
        @blur="$v.event.category.$touch()"
      />
      <template v-if="$v.event.title.$error">
        <p class="errorMessage" v-if="!$v.event.title.required">
          Title is required.
        </p>
      </template>
      <BaseInput
        class="field"
        label="Description"
        v-model="event.description"
        type="text"
        placeholder="Add a description"
        @blur="$v.event.description.$touch()"
      />
      <template v-if="$v.event.description.$error">
        <p class="errorMessage" v-if="!$v.event.description.required">
          Description is required.
        </p>
      </template>
      <h3>Where is your event?</h3>
      <BaseInput
        class="field"
        label="Location"
        v-model="event.location"
        type="text"
        placeholder="Add a location"
        :class="{ error: $v.event.title.$error }"
        @blur="$v.event.location.$touch()"
      />
      <template v-if="$v.event.location.$error">
        <p class="errorMessage" v-if="!$v.event.location.required">
          Location is required.
        </p>
      </template>
      <h3>When is your event?</h3>
      <div class="field">
        <label>Date</label>
        <datepicker
          v-model="event.date"
          placeholder="Select a date"
          :input-class="{ error: $v.event.date.$error }"
          @closed="$v.event.date.$touch()"
        />
      </div>
      <template v-if="$v.event.date.$error">
        <p class="errorMessage" v-if="!$v.event.date.required">
          Date is required.
        </p>
      </template>
      <BaseSelect
        class="field"
        label="Select a time"
        v-model="event.time"
        :options="times"
        @blur="$v.event.time.$touch()"
      />
      <template v-if="$v.event.time.$error">
        <p class="errorMessage" v-if="!$v.event.time.required">
          Time is required
        </p>
      </template>
      <BaseButton
        type="submit"
        buttonClass="-fill-gradient"
        :disabled="$v.$anyError"
        >Submit</BaseButton
      >
      <p v-if="$v.$anyError">Please fill out the required field(s).</p>
    </form>
  </div>
</template>

<script>
import Datepicker from 'vuejs-datepicker'
import NProgress from 'nprogress'
import { required } from 'vuelidate/lib/validators'

export default {
  components: {
    Datepicker
  },

  data() {
    const times = []
    for (let i = 0; i <= 24; i++) {
      times.push(i + ':00')
    }
    return {
      times,
      event: this.createFreshEventObject(),
      categories: this.$store.state.categories
    }
  },
  validations: {
    event: {
      title: { required },
      date: { required },
      time: { required },
      location: { required },
      description: { required },
      category: { required }
    }
  },

  methods: {
    createEvent() {
      this.$v.$touch()
      if (!this.$v.$invalid) {
        NProgress.start()
        this.$store
          .dispatch('event/createEvent', this.event)
          .then(() => {
            this.$router.push({
              name: 'event-show',
              params: { id: this.event.id }
            })
            this.event = this.createFreshEventObject()
          })
          .catch(() => {
            NProgress.done()
          })
      }
    },
    createFreshEventObject() {
      const id = Math.floor(Math.random() * 10000000)
      const user = this.$store.state.user.user

      return {
        id: id,
        user: user,
        title: '',
        date: '',
        time: '',
        location: '',
        description: '',
        organizer: user,
        category: '',
        attendees: []
      }
    }
  }
}
</script>

<style scoped>
.field {
  margin-bottom: 24px;
}
</style>
