<template>
  <div class="cms-vue-boilerplate-card">
    <p>Was this article helpful?</p>
    <div class="feedback-button-container" v-if="feedbackGiven == false">
      <button class="btn" v-on:click="sendFeedbackRating('Yes')">Yes</button>
      <button class="btn" v-on:click="sendFeedbackRating('No')">No</button>
    </div>
    <div class="feedback-button-container" v-else>
      <p>{{ ratingConfirmationMessage }}</p>
    </div>
    <div>
      <p>Leave article feedback</p>
      <form @submit.prevent="sendFeedbackComment" class="feedback-button-container">
        <textarea v-model="feedbackComment"></textarea>
        <button class="btn" type="submit">Submit</button>
      </form>
      <p>
        {{ confirmationMessage }}
      </p>
    </div>
  </div>
</template>

<script>
export default {
  name: 'ArticleFeedback',
  props: ['initialClickCount'],
  data: function() {
    return {
      reasonsCount: this.initialClickCount,
      feedbackGiven: false,
      feedbackRating: '',
      feedbackComment: '',
      confirmationMessage: '',
      ratingConfirmationMessage:'',
    };
  },
  computed: {
    reasonsText() {
      return `There are ${this.reasonsCount} to use HubSpot CMS + Vue!`;
    },
  },
  methods: {
    sendFeedbackRating(feedbackRating) {
      this.feedbackRating = feedbackRating;
      this.feedbackGiven = !this.feedbackGiven;
      let feedbackBody = {
        records: [
          {
            fields: {
              'Article URL': `${window.location.origin}${window.location.pathname}`,
              'Article Helpfulness': feedbackRating,
              'Date of Feedback': new Date().toISOString().substring(0, 10),
            },
          },
        ],
      };
      let fetchOptions = {
        method: 'POST',
        mode: 'cors',
        cache: 'no-cache', // *default, no-cache, reload, force-cache, only-if-cached
        credentials: 'same-origin', // include, *same-origin, omit
        headers: {
          'Content-Type': 'application/json',
        },
        body: JSON.stringify(feedbackBody),
      };
      fetch(
        'https://consulting.hubspot.com/_hcms/api/submit-article-rating',
        fetchOptions,
      )
        .then(response => response.json())
        .then(
          data => console.log(data),
          (this.ratingConfirmationMessage =
            'Thank you for the feedback! Consider leaving a commment to help make this article better.'),
        );
    },
    sendFeedbackComment() {
      let feedbackBody = {
        records: [
          {
            fields: {
              'Article URL': `${window.location.origin}${window.location.pathname}`,
              'Feedback Comment': this.feedbackComment,
              'Date of Feedback': new Date().toISOString().substring(0, 10),
            },
          },
        ],
      };
      let fetchOptions = {
        method: 'POST',
        mode: 'cors',
        credentials: 'same-origin', // include, *same-origin, omit
        headers: {
          'Content-Type': 'application/json',
        },
        body: JSON.stringify(feedbackBody),
      };
      fetch(
        'https://consulting.hubspot.com/_hcms/api/submit-article-feedback',
        fetchOptions,
      )
        .then(response => response.json())
        .then(
          data => console.log(data),
          (this.confirmationMessage =
            'Thank you for the feedback! It will be reviewed and incorporated.'),
        );
    },
  },
};
</script>

<style lang="scss">
.cms-vue-boilerplate-card {
  max-width: 500px;
  margin: 40px 20px;
  padding: 0.5em 1em;
  background-color: rgba(203, 202, 202, 0.1);
  border-radius: 6px;
  .btn {
    background-color: #ff7a59;
    border-radius: 3px;
    border: none;
    cursor: pointer;
    color: #ffffff;
    font-size: 1rem;
    padding: 11px 24px;
    text-align: center;
    user-select: none;
    transition: all 0.15s ease-out;
    display: inline-block;
    max-width: 100%;
    overflow: hidden;
    text-overflow: ellipsis;
    vertical-align: middle;
    white-space: nowrap;
  }
  .btn:hover {
    background-color: #f9a48e;
  }
  form.feedback-button-container {
    background-color: inherit;
    border: inherit;
    border-radius: inherit;
    padding: inherit;
  }
}
</style>
