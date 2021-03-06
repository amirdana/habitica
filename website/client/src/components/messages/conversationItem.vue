<template>
  <div
    class="conversation"
    :class="{active: activeKey === uuid}"
    @click="$emit('click', {})"
    @mouseleave="hideDropDown()"
  >
    <div class="user">
      <user-label
        :backer="backer"
        :contributor="contributor"
        :name="displayName"
      /><span
        v-if="username"
        class="username"
      >@{{ username }}</span>
      <div
        v-if="lastMessageDate"
        class="time"
      >
        {{ lastMessageDate | timeAgo }}
      </div>
    </div>
    <div class="preview-row">
      <div class="messagePreview">
        {{ lastMessageText }}
      </div>
      <div
        v-if="userLoggedIn.id !== uuid"
        class="actions"
      >
        <b-dropdown
          ref="dropdown"
          class="action-dropdown"
          right
          toggle-class="btn-flat action-padding"
          no-caret
          variant="link"
          size="lg"
        >
          <template slot="button-content">
            <div
              class="svg-icon inline dots"
              v-html="icons.dots"
            ></div>
          </template>
          <b-dropdown-item @click="block()">
            <span class="dropdown-icon-item">
              <div
                class="svg-icon inline"
                v-html="icons.remove"
              ></div><span class="text">{{ $t('block') }}</span></span>
          </b-dropdown-item>
        </b-dropdown>
      </div>
    </div>
  </div>
</template>

<script>
import moment from 'moment';
import userLabel from '../userLabel';

import dots from '@/assets/svg/dots.svg';
import remove from '@/assets/svg/remove.svg';

import { mapState } from '@/libs/store';

export default {
  components: {
    userLabel,
  },
  props: [
    'activeKey', 'uuid', 'backer', 'displayName',
    'username', 'contributor', 'lastMessageText',
    'lastMessageDate',
  ],
  computed: {
    ...mapState({
      userLoggedIn: 'user.data',
    }),
  },
  data () {
    return {
      icons: Object.freeze({
        dots,
        remove,
      }),
    };
  },
  filters: {
    timeAgo (value) {
      return moment(value).fromNow();
    },
  },
  methods: {
    hideDropDown () {
      const { dropdown } = this.$refs;

      if (dropdown) {
        dropdown.hide();
      }
    },
    block () {
      this.$store.dispatch('user:block', {
        uuid: this.uuid,
      });
    },
  },
};
</script>

<style lang="scss">
  @import '~@/assets/scss/colors.scss';

  .action-padding {
    height: 24px !important;
    width: 24px;
    padding: 0 !important;
  }

  .action-dropdown {
    .dropdown-item {
      padding: 12px 16px;

      .svg-icon {
        width: 16px;
        height: 16px;
      }
    }

    .dots {
      height: 16px;
      width: 4px;

      svg path {
        fill: $purple-300
      }
    }
  }
</style>

<style lang="scss" scoped>
  @import '~@/assets/scss/colors.scss';

  .conversation {
    padding: 1.5rem;
    border-bottom: 1px solid $gray-500;

    &:hover {
      cursor: pointer;
      background: #EEE;

      .actions {
        display: block;
      }
    }

    &.active {
      background-color: #f1edff;
    }

    .user {
      display: flex;
      flex-direction: row;
      height: 20px;

      .user-label {
        flex: 1;
        flex-grow: 0;
        margin-right: 0.5rem;
        white-space: nowrap;
      }

      .username {
        flex: 1;
        flex-grow: 0;
      }

      .time {
        flex: 2;
        text-align: end;
        overflow: hidden;
        text-overflow: ellipsis;
        white-space: nowrap;
        margin-left: 1rem;
      }
    }

    .messagePreview {
      //width: 100%;
      height: 30px;
      margin-right: 40px;
      margin-top: 4px;
      font-size: 12px;
      font-weight: normal;
      font-style: normal;
      font-stretch: normal;
      line-height: 1.33;
      letter-spacing: normal;
      color: $gray-100;
      overflow: hidden;

      // text-overflow: ellipsis;
    }
  }

  .preview-row {
    display: flex;
    flex-direction: row;
    position: relative;

    .messagePreview {
      flex: 1;
      width: calc(100% - 16px);
    }
  }

  .actions {
    position: absolute;
    right: 0;
    display: none;
    width: 16px;
    margin-top: 4px;

    .dots {
      height: 16px;
      width: 4px;
    }

    .action-icon {
      margin-right: 1em;
    }
  }
</style>
