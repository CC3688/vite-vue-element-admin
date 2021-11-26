<template>
  <div class="navbar">
    <hamburger
      id="hamburger-container"
      :is-active="sidebar.opened"
      class="hamburger-container"
      @toggleClick="toggleSideBar"
    />
    <breadcrumb id="breadcrumb-container" class="breadcrumb-container" />
    <div class="right-menu">
      <div
        v-permission="['message', 'message_list']"
        class="message"
        :class="{ dot: notifyCount }"
        @click="goMessage"
      >
        <i class="el-icon-message-solid" />
      </div>
      <el-dropdown
        szie="medium"
        class="avatar-container right-menu-item hover-effect"
        trigger="hover"
      >
        <div class="name">
          <i class="el-icon-user-solid" />
          <span>{{ name }}</span>
        </div>
        <el-dropdown-menu slot="dropdown">
          <el-dropdown-item>
            <span
              style="display: block; text-align: center; cursor: default"
              v-html="roles"
            />
          </el-dropdown-item>
          <el-dropdown-item divided @click.native="showDialog">
            <span style="display: block; text-align: center">重置密码</span>
          </el-dropdown-item>
          <el-dropdown-item @click.native="logout">
            <span style="display: block; text-align: center">退出登录</span>
          </el-dropdown-item>
        </el-dropdown-menu>
      </el-dropdown>
    </div>
    <ResetPassword :visible.sync="dialoVisile" />
  </div>
</template>

<script>
import { mapGetters } from 'vuex'
import Breadcrumb from '@/components/Breadcrumb'
import Hamburger from '@/components/Hamburger'
import ResetPassword from './ResetPassword.vue'
import { confirm } from '@/mixins'

export default {
  components: {
    Breadcrumb,
    Hamburger,
    ResetPassword
  },
  mixins: [confirm()],
  data() {
    return {
      dialoVisile: false
    }
  },
  computed: {
    ...mapGetters([
      'sidebar',
      'avatar',
      'device',
      'name',
      'roles',
      'notifyCount'
    ])
  },
  created() {
    this.$store.dispatch('user/getNotifyCount')
  },
  methods: {
    toggleSideBar() {
      this.$store.dispatch('app/toggleSideBar')
    },
    async logout() {
      this.confirm({
        title: '退出登录',
        tip: '确认退出登录？',
        action: async() => {
          await this.$store.dispatch('user/logout')
          this.$router.push(
            `/login?redirect=${encodeURIComponent(this.$route.fullPath)}`
          )
        }
      })
    },
    goMessage() {
      this.$router.push({ name: 'message' })
    },
    showDialog() {
      this.dialoVisile = true
    }
  }
}
</script>

<style lang="scss" scoped>
.navbar {
  height: 50px;
  overflow: hidden;
  position: relative;
  background: #fff;
  box-shadow: 0 1px 4px rgba(0, 21, 41, 0.08);

  .hamburger-container {
    line-height: 46px;
    height: 100%;
    float: left;
    cursor: pointer;
    transition: background 0.3s;
    -webkit-tap-highlight-color: transparent;

    &:hover {
      background: rgba(0, 0, 0, 0.025);
    }
  }

  .breadcrumb-container {
    float: left;
  }

  .errLog-container {
    display: inline-block;
    vertical-align: top;
  }

  .right-menu {
    float: right;
    height: 100%;
    line-height: 50px;
    display: flex;
    justify-content: flex-end;

    &:focus {
      outline: none;
    }
    .message {
      padding-right: 20px;
      cursor: pointer;
      i {
        font-size: 18px;
      }
      &.dot {
        position: relative;
        &::after {
          content: ' ';
          position: absolute;
          top: 13px;
          right: 14px;
          width: 8px;
          height: 8px;
          background: red;
          border-radius: 50%;
        }
      }
    }

    .right-menu-item {
      display: inline-block;
      padding: 0 8px;
      height: 100%;
      font-size: 18px;
      color: #5a5e66;
      vertical-align: text-bottom;

      &.hover-effect {
        cursor: pointer;
        transition: background 0.3s;

        &:hover {
          background: rgba(0, 0, 0, 0.025);
        }
      }
    }

    .avatar-container {
      margin-right: 15px;

      .name {
        i {
          font-size: 20px;
        }
        span {
          padding-left: 5px;
          font-size: 14px;
        }
      }
      .avatar-wrapper {
        margin-top: 5px;
        position: relative;

        .user-avatar {
          cursor: pointer;
          width: 40px;
          height: 40px;
          border-radius: 10px;
        }

        .el-icon-caret-bottom {
          cursor: pointer;
          position: absolute;
          right: -20px;
          top: 25px;
          font-size: 12px;
        }
      }
    }
  }
}
</style>
