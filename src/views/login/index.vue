<template>
  <div class="login">
    <div class="head">
      <img class="il_middle" src="../../imgs/logo.png" alt="">
      <span class="il_middle">DefiSafe</span>
    </div>
    <div class="box">
      <div class="title tc">{{$t('login.title')}}</div>
      <div class="sub-title tc">{{$t('login.desc')}}</div>
      <div class="b-foot">
        <button class="tc" @click="login">{{hasInstallWallet ? $t('login.connectBtn') : $t('login.installBtn')}}</button>
      </div>
    </div>
  </div>
</template>

<script>
  import { NET_IDS, CURRENT_NET } from '../../constants';
  export default {
    data() {
      return {
        hasInstallWallet: false
      };
    },
    created() {
      this.initData();
    },
    methods: {
      async initData() {
        this.hasInstallWallet = typeof window.ethereum === "undefined" ? false : true;

        if (this.hasInstallWallet) {
          let isLogin = await ethereum.request({ method: 'eth_accounts' });
          let netType = await ethereum.request({ method: 'net_version' });

          // Determine the current network type
          if ((NET_IDS[netType] == CURRENT_NET) && isLogin[0]) {
            this.$router.replace('/insure');
          }
        }
      },
      login() {
        if (!this.hasInstallWallet) {
          window.location.href = 'https://metamask.io/download.html';
          return;
        }

        let netType = window.ethereum.networkVersion;

        if (NET_IDS[netType] != CURRENT_NET) {
          this.$alert(this.$t('modal.login.desc'), this.$t('modal.login.title'), {
            confirmButtonText: this.$t('modal.login.btn')
          });
          return;
        }

        if (!window.ethereum.selectedAddress) {
          window.ethereum
            .enable()
            .then(accounts => {
              this.$router.replace("/insure");
            })
            .catch(reason => {
              alert("login fail");
            });
        } else {
          this.$router.replace("/insure");
        }
      }
    }
  };
</script>

<style lang="scss" scoped>
  @import './index.scss';
</style>