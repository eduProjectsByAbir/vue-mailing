<template>
  <div class="inbox-body">
    <button
      class="btn btn-danger pull-right"
      @click="data.message.isDeleted = true"
      :disabled="data.message.isDeleted"
    >
      <i class="fa fa-trash-o"></i> &nbsp;
      {{ data.message.isDeleted ? "Deleted" : "Delete" }}
    </button>
    <template v-if="typeof data.message.isRead !== 'undefined'">
      <button
        class="btn btn-warning pull-right"
        @click="data.message.isRead = false"
        :disabled="!data.message.isRead"
      >
        <i class="fa fa-envelope-open"></i> &nbsp;
      </button>
      <button
        class="btn btn-success pull-right"
        @click="data.message.isRead = true"
        :disabled="data.message.isRead"
      >
        <i class="fa fa-envelope"></i> &nbsp;
      </button>
    </template>
    <button class="btn btn-primary pull-right" @click="navigateBack">
      <i class="fa fa-arrow-left" aria-hidden="true"></i> &nbsp; Back
    </button>
    <p><strong>Date:</strong> {{ data.message.date.fromNow() }}</p>
    <p><strong>Subject:</strong> {{ data.message.from.name }}</p>
    <p>
      <strong>Subject:</strong> {{ data.message.from.name }} <{{
        data.message.from.email
      }}>
    </p>
    <hr />
    <div v-html="data.message.content" class="message"></div>
    <div v-if="data.message.attachments.length > 0" class="attachments">
      <h4>Attachments</h4>
      <ul>
        <li v-for="attachment in data.message.attachments">
          <i class="fa fa-paperclip"></i> {{ attachment.fileName }} ({{
            attachment.size | formatBytes
          }})
        </li>
      </ul>
    </div>
  </div>
</template>

<script>
import { eventBus } from "./main";

export default {
  props: {
    data: {
      type: Object,
      required: true,
    },
  },
  activated() {
    if (typeof this.data.message.isRead !== "undefined") {
      this.data.message.isRead = true;
    }
  },
  filters: {
    formatBytes(bytes) {
      if (bytes == 0) {
        return "0 Bytes";
      }

      let decimals = 2;
      let k = 1000;
      let dm = decimals + 1 || 3;
      let sizes = ["Bytes", "KB", "MB", "GB", "TB", "PB", "EB", "ZB", "YB"];
      let i = Math.floor(Math.log(bytes) / Math.log(k));

      return parseFloat((bytes / Math.pow(k, i)).toFixed(dm)) + " " + sizes[i];
    },
  },
  methods: {
    navigateBack() {
      let previousView = this.$parent.previousView;

      eventBus.$emit("changeView", {
        tag: previousView.tag,
        title: previousView.title,
        data: previousView.data,
      });
    },
  },
};
</script>
