<template>
  <div v-if="!comment.deleted" class="StoryComment" :class="{ 'is-collapsed': isCollapsed }" :data-id="comment.id">
    <div class="StoryComment-content">
      <div class="StoryComment-meta">
        <button class="StoryComment-collapseButton" @click="toggleCollapsed()"></button>
        <span class="StoryComment-author">{{ comment.by }}</span>
        <span class="StoryComment-date" :title="comment.time | getFormattedDate">{{ comment.time | getTimeSince }}</span>
      </div>
      <div class="StoryComment-body" v-html="`<p>${comment.text}`"></div>
    </div>

    <div class="StoryComment-responses">
      <story-comment v-for="subcomment in comment.kids" :key="subcomment" :id="subcomment"/>
    </div>
  </div>
</template>

<script>
import db from '@/db';
import { getTimeSince, getFormattedDate } from '@/filters';

export default {
  name: 'StoryComment',
  props: ['id'],
  data() {
    return {
      isCollapsed: false,
    };
  },
  firebase() {
    return {
      comment: {
        source: db.ref(`/v0/item/${this.id}`),
        asObject: true,
      },
    };
  },
  filters: {
    getTimeSince,
    getFormattedDate,
  },
  methods: {
    toggleCollapsed() {
      this.isCollapsed = !this.isCollapsed;
    },
  },
};
</script>

<style lang="less" scoped>
@import "../definitions";

.StoryComment {
  padding: @spacing-lg;
  border-top: 1px solid @app-bg;

  &-meta {
    font-size: 0.8125em;
  }

  &-collapseButton {
    padding: 0;
    background: none;
    border: 0;
    color: @faint-text;
    cursor: pointer;
    font-family: monospace;

    &::before {
      content: '[-]';
    }
  }

  &-date {
    color: @faint-text;
  }

  &-author {
    font-weight: bold;
  }

  &-body {
    margin-bottom: @spacing-lg;
    font-size: 0.875em;
    line-height: 1.5;
    overflow-wrap: break-word;

    /deep/ p {
      margin: 0.75em 0;
    }

    /deep/ pre {
      overflow-x: auto;
    }
  }

  &-responses {
    margin-bottom: @spacing-lg * -1;
  }

  &.is-collapsed {
    .StoryComment-body,
    .StoryComment-responses {
      display: none;
    }

    .StoryComment-collapseButton::before {
      content: '[+]';
    }
  }
}
</style>
