<template>
  <div class="login-container">
    <el-form class="login-form" autoComplete="on" :model="loginForm" :rules="loginRules" ref="loginForm"
             label-position="left">
      <div class="title-container">
        <h3 class="title">{{$t('login.title')}}</h3>
        <lang-select class="set-language"></lang-select>
      </div>
      <el-form-item prop="username">
        <span class="svg-container svg-container_login">
          <svg-icon icon-class="user" />
        </span>
        <el-input name="username" type="text" v-model="loginForm.username" autoComplete="on" placeholder="username" />
      </el-form-item>

      <el-form-item prop="password">
        <span class="svg-container">
          <svg-icon icon-class="password" />
        </span>
        <el-input name="password" :type="passwordType" @keyup.enter.native="handleLogin" v-model="loginForm.password"
                  autoComplete="on" placeholder="password" />
        <span class="show-pwd" @click="showPwd">
          <svg-icon icon-class="eye" />
        </span>
      </el-form-item>

      <el-button type="primary" style="width:100%;margin-bottom:30px;" :loading="loading"
                 @click.native.prevent="handleLogin">{{$t('login.logIn')}}
      </el-button>

      <div class="tips">
        <span>{{$t('login.username')}} : admin</span>
        <span>{{$t('login.password')}} : {{$t('login.any')}}</span>
      </div>
      <div class="tips">
        <span style="margin-right:18px;">{{$t('login.username')}} : editor</span>
        <span>{{$t('login.password')}} : {{$t('login.any')}}</span>
      </div>

      <el-button class="thirdparty-button" type="primary" @click="showDialog=true">{{$t('login.thirdparty')}}
      </el-button>

    </el-form>
    <div class="copyright">
      <a href="//github.com/truexin1292" target="_blank">truexinology.site</a> 版权所有
      <br />
      粤ICP备17145022号-1
    </div>
    <div class="text-wrapper">
      <div class="text part1">
        <div>
          <span class="letter poofed"><div class="character">t</div> <span></span></span>
          <span class="letter"><div class="character">r</div> <span></span></span>
          <span class="letter"><div class="character">u</div> <span></span></span>
          <span class="letter"><div class="character">e</div> <span></span></span>
          <span class="letter poofed"><div class="character">x</div> <span></span></span>
          <span class="letter"><div class="character">i</div> <span></span></span>
          <span class="letter"><div class="character">n</div> <span></span></span>
        </div>
      </div>
      <div class="text part2">
        <div>
                <span class="letter poofed">
                    <div class="character">’s</div>
                    <span></span>
                </span>
          <span class="letter poofed">
                    <div class="character">w</div>
                    <span></span>
                </span>
          <span class="letter">
                    <div class="character">e</div>
                    <span></span>
                </span>
          <span class="letter">
                    <div class="character">b</div>
                    <span></span>
                </span>
          <span class="letter poofed">
                    <div class="character">s</div>
                    <span></span>
                </span>
          <span class="letter">
                    <div class="character">i</div>
                <span></span>
                </span>
          <span class="letter poofed">
                    <div class="character">t</div>
                    <span></span>
                </span>
          <span class="letter poofed">
                    <div class="character">e</div>
                    <span></span>
                </span>
        </div>
      </div>
    </div>

    <el-dialog :title="$t('login.thirdparty')" :visible.sync="showDialog" append-to-body>
      {{$t('login.thirdpartyTips')}}
      <br />
      <br />
      <br />
      <social-sign />
    </el-dialog>

  </div>
</template>

<script>
  import { isvalidUsername } from '@/utils/validate'
  import LangSelect from '@/components/LangSelect'
  import SocialSign from './socialsignin'
  // import { getQueryObject } from '@/utils'

  export default {
    components: { LangSelect, SocialSign },
    name: 'login',
    data() {
      const validateUsername = (rule, value, callback) => {
        if (!isvalidUsername(value)) {
          callback(new Error('请输入正确的用户名！'))
        } else {
          callback()
        }
      }
      const validatePassword = (rule, value, callback) => {
        // if ((value === '123456' && this.loginForm.username === 'truexin') || (this.loginForm.username === 'vue') && value) {
        if (value.length < 6) {
          callback(new Error('请输入正确的密码！'))
        } else {
          callback()
        }
      }
      return {
        loginForm: {
          username: 'truexin',
          password: '123456'
        },
        loginRules: {
          username: [{ required: true, trigger: 'blur', validator: validateUsername }],
          password: [{ required: true, trigger: 'blur', validator: validatePassword }]
        },
        passwordType: 'password',
        loading: false,
        showDialog: false,
        // 动画属性开始
        clickTimes: 0,
        word1: {
          id: 1,
          letters: [
            { char: 't', alive: true },
            { char: 'r', alive: true },
            { char: 'u', alive: true },
            { char: 'e', alive: true },
            { char: 'x', alive: true },
            { char: 'i', alive: true },
            { char: 'n', alive: true }
          ]
        },
        word2: {
          id: 2,
          letters: [
            { char: '’s', alive: true },
            { char: 'w', alive: true },
            { char: 'e', alive: true },
            { char: 'b', alive: true },
            { char: 's', alive: true },
            { char: 'i', alive: true },
            { char: 't', alive: true },
            { char: 'e', alive: true }
          ]
        },
        totalLetters: 0
      }
    },
    mounted() {
      this.totalLetters = this.word1.letters.length + this.word2.letters.length
    },
    methods: {
      showPwd() {
        if (this.passwordType === 'password') {
          this.passwordType = ''
        } else {
          this.passwordType = 'password'
        }
      },
      handleLogin() {
        this.$refs.loginForm.validate(valid => {
          if (valid) {
            this.loading = true
            this.$store.dispatch('LoginByUsername', this.loginForm).then(() => {
              this.loading = false
              this.$router.push({ path: '/' })
            }).catch(() => {
              this.$message('登录失败')
              this.loading = false
            })
          } else {
            console.log('error submit!!')
            return false
          }
        })
      },
      // afterQRScan() {
      //   const hash = window.location.hash.slice(1)
      //   const hashObj = getQueryObject(hash)
      //   const originUrl = window.location.origin
      //   history.replaceState({}, '', originUrl)
      //   const codeMap = {
      //     wechat: 'code',
      //     tencent: 'code'
      //   }
      //   const codeName = hashObj[codeMap[this.auth_type]]
      //   if (!codeName) {
      //     this.$message('第三方登录失败')
      //   } else {
      //     this.$store.dispatch('LoginByThirdparty', codeName).then(() => {
      //       this.$router.push({ path: '/' })
      //     })
      //   }
      // },
      // 动画开始
      rem(id, letter) {
        // update text
        if (!this.clicked) {
          this.clickTimes++
        }

        // word 1
        if (id === 1) {
          this.word1.letters = this.word1.letters.map((item) => {
            if (item.char === letter) {
              item.alive = false
            }
            return item
          })
        } else if (id === 2) {
          // word 2
          this.word2.letters = this.word2.letters.map((item) => {
            if (item.char === letter && item.alive !== false) {
              item.alive = false
              letter = null
            }
            return item
          })
        }
      },
      back() {
        // Reset text
        this.clickTimes = 0

        // Restore letter position
        this.word1.letters = this.word1.letters.map((item) => {
          item.alive = true
          return item
        })
        this.word2.letters = this.word2.letters.map((item) => {
          item.alive = true
          return item
        })
      }
    },
    created() {
      // window.addEventListener('hashchange', this.afterQRScan)
    },
    destroyed() {
      // window.removeEventListener('hashchange', this.afterQRScan)
    }
  }
</script>

<style rel="stylesheet/scss" lang="scss">
  $bg: #2d3a4b;
  $light_gray: #eee;

  /* reset element-ui css */
  .login-container {
    .el-input {
      display: inline-block;
      height: 47px;
      width: 85%;
      input {
        background: transparent;
        border: 0px;
        -webkit-appearance: none;
        border-radius: 0px;
        padding: 12px 5px 12px 15px;
        color: $light_gray;
        height: 47px;
        &:-webkit-autofill {
          -webkit-box-shadow: 0 0 0px 1000px $bg inset !important;
          -webkit-text-fill-color: #fff !important;
        }
      }
    }
    .el-form-item {
      border: 1px solid rgba(255, 255, 255, 0.1);
      background: rgba(0, 0, 0, 0.1);
      border-radius: 5px;
      color: #454545;
    }
  }
</style>

<style rel="stylesheet/scss" lang="scss" scoped>
  $bg: #2d3a4b;
  $dark_gray: #889aa4;
  $light_gray: #eee;

  .login-container {
    position: fixed;
    height: 100%;
    width: 100%;
    background-color: $bg;
    .login-form {
      position: absolute;
      left: 0;
      right: 0;
      width: 520px;
      padding: 35px 35px 15px 35px;
      margin: 120px auto;
    }
    .tips {
      font-size: 14px;
      color: #fff;
      margin-bottom: 10px;
      span {
        &:first-of-type {
          margin-right: 16px;
        }
      }
    }
    .svg-container {
      padding: 6px 5px 6px 15px;
      color: $dark_gray;
      vertical-align: middle;
      width: 30px;
      display: inline-block;
      &_login {
        font-size: 20px;
      }
    }
    .title-container {
      position: relative;
      .title {
        font-size: 26px;
        font-weight: 400;
        color: $light_gray;
        margin: 0px auto 40px auto;
        text-align: center;
        font-weight: bold;
      }
      .set-language {
        color: #fff;
        position: absolute;
        top: 5px;
        right: 0px;
      }
    }
    .show-pwd {
      position: absolute;
      right: 10px;
      top: 7px;
      font-size: 16px;
      color: $dark_gray;
      cursor: pointer;
      user-select: none;
    }
    .thirdparty-button {
      position: absolute;
      right: 35px;
      bottom: 28px;
    }

    .copyright {
      position: absolute;
      bottom: 10px;
      text-align: center;
      width: 100%;
      color: #fff;
    }
    // 动画样式开始
    .text-wrapper {
      padding: 0 1rem;
      width: 100%;
      text-align: center;
      position: absolute;
      bottom: 50px;
      font-size: .8em;
    }

    .text {
      font-size: 6em;
      text-transform: uppercase;
      letter-spacing: -14px;
    }

    .text .letter {
      position: relative;
      display: inline-block;
      -webkit-transition: all .4s;
      transition: all .4s;
    }

    .text .letter .character {
      opacity: 0.65;
      -webkit-transition: color .4s;
      transition: color .4s;
      cursor: pointer;
    }

    .text .letter span {
      position: absolute;
      bottom: .8rem;
      left: .4rem;
      display: block;
      width: 100%;
      height: 15px;
    }

    .text .letter span::before {
      content: '';
      position: absolute;
      top: 50%;
      left: 50%;
      width: 0;
      height: 0;
      -webkit-transform: translate(-50%, -50%);
      transform: translate(-50%, -50%);
      border-radius: 50%;
      background: rgba(0, 0, 0, 0.25);
    }

    .text .letter:hover .character {
      color: #fff !important;
    }

    .text.part1 .letter:nth-child(1).poofed {
      -webkit-transform-origin: 50% 50%;
      transform-origin: 50% 50%;
      -webkit-animation: poofing1 1.4s ease-in-out infinite alternate;
      animation: poofing1 1.4s ease-in-out infinite alternate;
    }

    @-webkit-keyframes poofing1 {
      0% {
        -webkit-transform: rotateZ(0deg) rotateY(0) translateY(0px) scale(1);
        transform: rotateZ(0deg) rotateY(0) translateY(0px) scale(1);
      }
      50% {
        -webkit-transform: rotateZ(0deg) rotateY(360deg) translateY(0px) scale(1);
        transform: rotateZ(0deg) rotateY(360deg) translateY(0px) scale(1);
      }
      56% {
        -webkit-transform: rotateZ(459deg) rotateY(360deg) translateY(0px) scale(1);
        transform: rotateZ(459deg) rotateY(360deg) translateY(0px) scale(1);
      }
      100% {
        -webkit-transform: rotateZ(459deg) rotateY(360deg) translateY(-700px) scale(0.01);
        transform: rotateZ(459deg) rotateY(360deg) translateY(-700px) scale(0.01);
      }
    }

    @keyframes poofing1 {
      0% {
        -webkit-transform: rotateZ(0deg) rotateY(0) translateY(0px) scale(1);
        transform: rotateZ(0deg) rotateY(0) translateY(0px) scale(1);
      }
      50% {
        -webkit-transform: rotateZ(0deg) rotateY(360deg) translateY(0px) scale(1);
        transform: rotateZ(0deg) rotateY(360deg) translateY(0px) scale(1);
      }
      56% {
        -webkit-transform: rotateZ(459deg) rotateY(360deg) translateY(0px) scale(1);
        transform: rotateZ(459deg) rotateY(360deg) translateY(0px) scale(1);
      }
      100% {
        -webkit-transform: rotateZ(459deg) rotateY(360deg) translateY(-700px) scale(0.01);
        transform: rotateZ(459deg) rotateY(360deg) translateY(-700px) scale(0.01);
      }
    }

    .text.part1 .letter:nth-child(1) .character {
      color: #4e2a84;
      -webkit-animation: wave 1.2s linear infinite;
      animation: wave 1.2s linear infinite;
      -webkit-animation-delay: 0.33333s;
      animation-delay: 0.33333s;
    }

    .text.part1 .letter:nth-child(1) span::before {
      -webkit-animation: shadow 1.2s linear infinite;
      animation: shadow 1.2s linear infinite;
      -webkit-animation-delay: 0.33333s;
      animation-delay: 0.33333s;
    }

    .text.part1 .letter:nth-child(2).poofed {
      -webkit-transform-origin: 50% 50%;
      transform-origin: 50% 50%;
      -webkit-animation: poofing2 1.4s ease-in-out infinite alternate;
      animation: poofing2 1.4s ease-in-out infinite alternate;
    }

    @-webkit-keyframes poofing2 {
      0% {
        -webkit-transform: rotateZ(0deg) rotateY(0) translateY(0px) scale(1);
        transform: rotateZ(0deg) rotateY(0) translateY(0px) scale(1);
      }
      50% {
        -webkit-transform: rotateZ(0deg) rotateY(360deg) translateY(0px) scale(1);
        transform: rotateZ(0deg) rotateY(360deg) translateY(0px) scale(1);
      }
      56% {
        -webkit-transform: rotateZ(540deg) rotateY(360deg) translateY(0px) scale(1);
        transform: rotateZ(540deg) rotateY(360deg) translateY(0px) scale(1);
      }
      100% {
        -webkit-transform: rotateZ(540deg) rotateY(360deg) translateY(-700px) scale(0.01);
        transform: rotateZ(540deg) rotateY(360deg) translateY(-700px) scale(0.01);
      }
    }

    @keyframes poofing2 {
      0% {
        -webkit-transform: rotateZ(0deg) rotateY(0) translateY(0px) scale(1);
        transform: rotateZ(0deg) rotateY(0) translateY(0px) scale(1);
      }
      50% {
        -webkit-transform: rotateZ(0deg) rotateY(360deg) translateY(0px) scale(1);
        transform: rotateZ(0deg) rotateY(360deg) translateY(0px) scale(1);
      }
      56% {
        -webkit-transform: rotateZ(540deg) rotateY(360deg) translateY(0px) scale(1);
        transform: rotateZ(540deg) rotateY(360deg) translateY(0px) scale(1);
      }
      100% {
        -webkit-transform: rotateZ(540deg) rotateY(360deg) translateY(-700px) scale(0.01);
        transform: rotateZ(540deg) rotateY(360deg) translateY(-700px) scale(0.01);
      }
    }

    .text.part1 .letter:nth-child(2) .character {
      color: #ff5a5f;
      -webkit-animation: wave 1.2s linear infinite;
      animation: wave 1.2s linear infinite;
      -webkit-animation-delay: 0.66667s;
      animation-delay: 0.66667s;
    }

    .text.part1 .letter:nth-child(2) span::before {
      -webkit-animation: shadow 1.2s linear infinite;
      animation: shadow 1.2s linear infinite;
      -webkit-animation-delay: 0.66667s;
      animation-delay: 0.66667s;
    }

    .text.part1 .letter:nth-child(3).poofed {
      -webkit-transform-origin: 50% 50%;
      transform-origin: 50% 50%;
      -webkit-animation: poofing3 1.4s ease-in-out infinite alternate;
      animation: poofing3 1.4s ease-in-out infinite alternate;
    }

    @-webkit-keyframes poofing3 {
      0% {
        -webkit-transform: rotateZ(0deg) rotateY(0) translateY(0px) scale(1);
        transform: rotateZ(0deg) rotateY(0) translateY(0px) scale(1);
      }
      50% {
        -webkit-transform: rotateZ(0deg) rotateY(360deg) translateY(0px) scale(1);
        transform: rotateZ(0deg) rotateY(360deg) translateY(0px) scale(1);
      }
      56% {
        -webkit-transform: rotateZ(264deg) rotateY(360deg) translateY(0px) scale(1);
        transform: rotateZ(264deg) rotateY(360deg) translateY(0px) scale(1);
      }
      100% {
        -webkit-transform: rotateZ(264deg) rotateY(360deg) translateY(-700px) scale(0.01);
        transform: rotateZ(264deg) rotateY(360deg) translateY(-700px) scale(0.01);
      }
    }

    @keyframes poofing3 {
      0% {
        -webkit-transform: rotateZ(0deg) rotateY(0) translateY(0px) scale(1);
        transform: rotateZ(0deg) rotateY(0) translateY(0px) scale(1);
      }
      50% {
        -webkit-transform: rotateZ(0deg) rotateY(360deg) translateY(0px) scale(1);
        transform: rotateZ(0deg) rotateY(360deg) translateY(0px) scale(1);
      }
      56% {
        -webkit-transform: rotateZ(264deg) rotateY(360deg) translateY(0px) scale(1);
        transform: rotateZ(264deg) rotateY(360deg) translateY(0px) scale(1);
      }
      100% {
        -webkit-transform: rotateZ(264deg) rotateY(360deg) translateY(-700px) scale(0.01);
        transform: rotateZ(264deg) rotateY(360deg) translateY(-700px) scale(0.01);
      }
    }

    .text.part1 .letter:nth-child(3) .character {
      color: #f99b0c;
      -webkit-animation: wave 1.2s linear infinite;
      animation: wave 1.2s linear infinite;
      -webkit-animation-delay: 1s;
      animation-delay: 1s;
    }

    .text.part1 .letter:nth-child(3) span::before {
      -webkit-animation: shadow 1.2s linear infinite;
      animation: shadow 1.2s linear infinite;
      -webkit-animation-delay: 1s;
      animation-delay: 1s;
    }

    .text.part1 .letter:nth-child(4).poofed {
      -webkit-transform-origin: 50% 50%;
      transform-origin: 50% 50%;
      -webkit-animation: poofing4 1.4s ease-in-out infinite alternate;
      animation: poofing4 1.4s ease-in-out infinite alternate;
    }

    @-webkit-keyframes poofing4 {
      0% {
        -webkit-transform: rotateZ(0deg) rotateY(0) translateY(0px) scale(1);
        transform: rotateZ(0deg) rotateY(0) translateY(0px) scale(1);
      }
      50% {
        -webkit-transform: rotateZ(0deg) rotateY(360deg) translateY(0px) scale(1);
        transform: rotateZ(0deg) rotateY(360deg) translateY(0px) scale(1);
      }
      56% {
        -webkit-transform: rotateZ(42deg) rotateY(360deg) translateY(0px) scale(1);
        transform: rotateZ(42deg) rotateY(360deg) translateY(0px) scale(1);
      }
      100% {
        -webkit-transform: rotateZ(42deg) rotateY(360deg) translateY(-700px) scale(0.01);
        transform: rotateZ(42deg) rotateY(360deg) translateY(-700px) scale(0.01);
      }
    }

    @keyframes poofing4 {
      0% {
        -webkit-transform: rotateZ(0deg) rotateY(0) translateY(0px) scale(1);
        transform: rotateZ(0deg) rotateY(0) translateY(0px) scale(1);
      }
      50% {
        -webkit-transform: rotateZ(0deg) rotateY(360deg) translateY(0px) scale(1);
        transform: rotateZ(0deg) rotateY(360deg) translateY(0px) scale(1);
      }
      56% {
        -webkit-transform: rotateZ(42deg) rotateY(360deg) translateY(0px) scale(1);
        transform: rotateZ(42deg) rotateY(360deg) translateY(0px) scale(1);
      }
      100% {
        -webkit-transform: rotateZ(42deg) rotateY(360deg) translateY(-700px) scale(0.01);
        transform: rotateZ(42deg) rotateY(360deg) translateY(-700px) scale(0.01);
      }
    }

    .text.part1 .letter:nth-child(4) .character {
      color: #cee007;
      -webkit-animation: wave 1.2s linear infinite;
      animation: wave 1.2s linear infinite;
      -webkit-animation-delay: 1.33333s;
      animation-delay: 1.33333s;
    }

    .text.part1 .letter:nth-child(4) span::before {
      -webkit-animation: shadow 1.2s linear infinite;
      animation: shadow 1.2s linear infinite;
      -webkit-animation-delay: 1.33333s;
      animation-delay: 1.33333s;
    }

    .text.part1 .letter:nth-child(5).poofed {
      -webkit-transform-origin: 50% 50%;
      transform-origin: 50% 50%;
      -webkit-animation: poofing5 1.4s ease-in-out infinite alternate;
      animation: poofing5 1.4s ease-in-out infinite alternate;
    }

    @-webkit-keyframes poofing5 {
      0% {
        -webkit-transform: rotateZ(0deg) rotateY(0) translateY(0px) scale(1);
        transform: rotateZ(0deg) rotateY(0) translateY(0px) scale(1);
      }
      50% {
        -webkit-transform: rotateZ(0deg) rotateY(360deg) translateY(0px) scale(1);
        transform: rotateZ(0deg) rotateY(360deg) translateY(0px) scale(1);
      }
      56% {
        -webkit-transform: rotateZ(384deg) rotateY(360deg) translateY(0px) scale(1);
        transform: rotateZ(384deg) rotateY(360deg) translateY(0px) scale(1);
      }
      100% {
        -webkit-transform: rotateZ(384deg) rotateY(360deg) translateY(-700px) scale(0.01);
        transform: rotateZ(384deg) rotateY(360deg) translateY(-700px) scale(0.01);
      }
    }

    @keyframes poofing5 {
      0% {
        -webkit-transform: rotateZ(0deg) rotateY(0) translateY(0px) scale(1);
        transform: rotateZ(0deg) rotateY(0) translateY(0px) scale(1);
      }
      50% {
        -webkit-transform: rotateZ(0deg) rotateY(360deg) translateY(0px) scale(1);
        transform: rotateZ(0deg) rotateY(360deg) translateY(0px) scale(1);
      }
      56% {
        -webkit-transform: rotateZ(384deg) rotateY(360deg) translateY(0px) scale(1);
        transform: rotateZ(384deg) rotateY(360deg) translateY(0px) scale(1);
      }
      100% {
        -webkit-transform: rotateZ(384deg) rotateY(360deg) translateY(-700px) scale(0.01);
        transform: rotateZ(384deg) rotateY(360deg) translateY(-700px) scale(0.01);
      }
    }

    .text.part1 .letter:nth-child(5) .character {
      color: #00c6b2;
      -webkit-animation: wave 1.2s linear infinite;
      animation: wave 1.2s linear infinite;
      -webkit-animation-delay: 1.66667s;
      animation-delay: 1.66667s;
    }

    .text.part1 .letter:nth-child(5) span::before {
      -webkit-animation: shadow 1.2s linear infinite;
      animation: shadow 1.2s linear infinite;
      -webkit-animation-delay: 1.66667s;
      animation-delay: 1.66667s;
    }

    .text.part1 .letter:nth-child(6).poofed {
      -webkit-transform-origin: 50% 50%;
      transform-origin: 50% 50%;
      -webkit-animation: poofing6 1.4s ease-in-out infinite alternate;
      animation: poofing6 1.4s ease-in-out infinite alternate;
    }

    @-webkit-keyframes poofing6 {
      0% {
        -webkit-transform: rotateZ(0deg) rotateY(0) translateY(0px) scale(1);
        transform: rotateZ(0deg) rotateY(0) translateY(0px) scale(1);
      }
      50% {
        -webkit-transform: rotateZ(0deg) rotateY(360deg) translateY(0px) scale(1);
        transform: rotateZ(0deg) rotateY(360deg) translateY(0px) scale(1);
      }
      56% {
        -webkit-transform: rotateZ(156deg) rotateY(360deg) translateY(0px) scale(1);
        transform: rotateZ(156deg) rotateY(360deg) translateY(0px) scale(1);
      }
      100% {
        -webkit-transform: rotateZ(156deg) rotateY(360deg) translateY(-700px) scale(0.01);
        transform: rotateZ(156deg) rotateY(360deg) translateY(-700px) scale(0.01);
      }
    }

    @keyframes poofing6 {
      0% {
        -webkit-transform: rotateZ(0deg) rotateY(0) translateY(0px) scale(1);
        transform: rotateZ(0deg) rotateY(0) translateY(0px) scale(1);
      }
      50% {
        -webkit-transform: rotateZ(0deg) rotateY(360deg) translateY(0px) scale(1);
        transform: rotateZ(0deg) rotateY(360deg) translateY(0px) scale(1);
      }
      56% {
        -webkit-transform: rotateZ(156deg) rotateY(360deg) translateY(0px) scale(1);
        transform: rotateZ(156deg) rotateY(360deg) translateY(0px) scale(1);
      }
      100% {
        -webkit-transform: rotateZ(156deg) rotateY(360deg) translateY(-700px) scale(0.01);
        transform: rotateZ(156deg) rotateY(360deg) translateY(-700px) scale(0.01);
      }
    }

    .text.part1 .letter:nth-child(6) .character {
      color: #4e2a84;
      -webkit-animation: wave 1.2s linear infinite;
      animation: wave 1.2s linear infinite;
      -webkit-animation-delay: 2s;
      animation-delay: 2s;
    }

    .text.part1 .letter:nth-child(6) span::before {
      -webkit-animation: shadow 1.2s linear infinite;
      animation: shadow 1.2s linear infinite;
      -webkit-animation-delay: 2s;
      animation-delay: 2s;
    }

    .text.part2 span:nth-child(1).poofed {
      -webkit-animation: sec_poofing1 1.4s ease-in-out infinite alternate;
      animation: sec_poofing1 1.4s ease-in-out infinite alternate;
    }

    @-webkit-keyframes sec_poofing1 {
      0% {
        -webkit-transform: rotateZ(0deg) rotateY(0) translateY(0px) scale(1);
        transform: rotateZ(0deg) rotateY(0) translateY(0px) scale(1);
      }
      50% {
        -webkit-transform: rotateZ(0deg) rotateY(360deg) translateY(0px) scale(1);
        transform: rotateZ(0deg) rotateY(360deg) translateY(0px) scale(1);
      }
      56% {
        -webkit-transform: rotateZ(268deg) rotateY(360deg) translateY(0px) scale(1);
        transform: rotateZ(268deg) rotateY(360deg) translateY(0px) scale(1);
      }
      100% {
        -webkit-transform: rotateZ(268deg) rotateY(360deg) translateY(-700px) scale(0.01);
        transform: rotateZ(268deg) rotateY(360deg) translateY(-700px) scale(0.01);
      }
    }

    @keyframes sec_poofing1 {
      0% {
        -webkit-transform: rotateZ(0deg) rotateY(0) translateY(0px) scale(1);
        transform: rotateZ(0deg) rotateY(0) translateY(0px) scale(1);
      }
      50% {
        -webkit-transform: rotateZ(0deg) rotateY(360deg) translateY(0px) scale(1);
        transform: rotateZ(0deg) rotateY(360deg) translateY(0px) scale(1);
      }
      56% {
        -webkit-transform: rotateZ(268deg) rotateY(360deg) translateY(0px) scale(1);
        transform: rotateZ(268deg) rotateY(360deg) translateY(0px) scale(1);
      }
      100% {
        -webkit-transform: rotateZ(268deg) rotateY(360deg) translateY(-700px) scale(0.01);
        transform: rotateZ(268deg) rotateY(360deg) translateY(-700px) scale(0.01);
      }
    }

    .text.part2 span:nth-child(1) .character {
      color: #ff5a5f;
      -webkit-animation: wave 1.2s linear infinite;
      animation: wave 1.2s linear infinite;
      -webkit-animation-delay: 2.33333s;
      animation-delay: 2.33333s;
    }

    .text.part2 span:nth-child(1) span::before {
      -webkit-animation: shadow 1.2s linear infinite;
      animation: shadow 1.2s linear infinite;
      -webkit-animation-delay: 2.33333s;
      animation-delay: 2.33333s;
    }

    .text.part2 span:nth-child(2).poofed {
      -webkit-animation: sec_poofing2 1.4s ease-in-out infinite alternate;
      animation: sec_poofing2 1.4s ease-in-out infinite alternate;
    }

    @-webkit-keyframes sec_poofing2 {
      0% {
        -webkit-transform: rotateZ(0deg) rotateY(0) translateY(0px) scale(1);
        transform: rotateZ(0deg) rotateY(0) translateY(0px) scale(1);
      }
      50% {
        -webkit-transform: rotateZ(0deg) rotateY(360deg) translateY(0px) scale(1);
        transform: rotateZ(0deg) rotateY(360deg) translateY(0px) scale(1);
      }
      56% {
        -webkit-transform: rotateZ(135deg) rotateY(360deg) translateY(0px) scale(1);
        transform: rotateZ(135deg) rotateY(360deg) translateY(0px) scale(1);
      }
      100% {
        -webkit-transform: rotateZ(135deg) rotateY(360deg) translateY(-700px) scale(0.01);
        transform: rotateZ(135deg) rotateY(360deg) translateY(-700px) scale(0.01);
      }
    }

    @keyframes sec_poofing2 {
      0% {
        -webkit-transform: rotateZ(0deg) rotateY(0) translateY(0px) scale(1);
        transform: rotateZ(0deg) rotateY(0) translateY(0px) scale(1);
      }
      50% {
        -webkit-transform: rotateZ(0deg) rotateY(360deg) translateY(0px) scale(1);
        transform: rotateZ(0deg) rotateY(360deg) translateY(0px) scale(1);
      }
      56% {
        -webkit-transform: rotateZ(135deg) rotateY(360deg) translateY(0px) scale(1);
        transform: rotateZ(135deg) rotateY(360deg) translateY(0px) scale(1);
      }
      100% {
        -webkit-transform: rotateZ(135deg) rotateY(360deg) translateY(-700px) scale(0.01);
        transform: rotateZ(135deg) rotateY(360deg) translateY(-700px) scale(0.01);
      }
    }

    .text.part2 span:nth-child(2) .character {
      color: #f99b0c;
      -webkit-animation: wave 1.2s linear infinite;
      animation: wave 1.2s linear infinite;
      -webkit-animation-delay: 2.66667s;
      animation-delay: 2.66667s;
    }

    .text.part2 span:nth-child(2) span::before {
      -webkit-animation: shadow 1.2s linear infinite;
      animation: shadow 1.2s linear infinite;
      -webkit-animation-delay: 2.66667s;
      animation-delay: 2.66667s;
    }

    .text.part2 span:nth-child(3).poofed {
      -webkit-animation: sec_poofing3 1.4s ease-in-out infinite alternate;
      animation: sec_poofing3 1.4s ease-in-out infinite alternate;
    }

    @-webkit-keyframes sec_poofing3 {
      0% {
        -webkit-transform: rotateZ(0deg) rotateY(0) translateY(0px) scale(1);
        transform: rotateZ(0deg) rotateY(0) translateY(0px) scale(1);
      }
      50% {
        -webkit-transform: rotateZ(0deg) rotateY(360deg) translateY(0px) scale(1);
        transform: rotateZ(0deg) rotateY(360deg) translateY(0px) scale(1);
      }
      56% {
        -webkit-transform: rotateZ(442deg) rotateY(360deg) translateY(0px) scale(1);
        transform: rotateZ(442deg) rotateY(360deg) translateY(0px) scale(1);
      }
      100% {
        -webkit-transform: rotateZ(442deg) rotateY(360deg) translateY(-700px) scale(0.01);
        transform: rotateZ(442deg) rotateY(360deg) translateY(-700px) scale(0.01);
      }
    }

    @keyframes sec_poofing3 {
      0% {
        -webkit-transform: rotateZ(0deg) rotateY(0) translateY(0px) scale(1);
        transform: rotateZ(0deg) rotateY(0) translateY(0px) scale(1);
      }
      50% {
        -webkit-transform: rotateZ(0deg) rotateY(360deg) translateY(0px) scale(1);
        transform: rotateZ(0deg) rotateY(360deg) translateY(0px) scale(1);
      }
      56% {
        -webkit-transform: rotateZ(442deg) rotateY(360deg) translateY(0px) scale(1);
        transform: rotateZ(442deg) rotateY(360deg) translateY(0px) scale(1);
      }
      100% {
        -webkit-transform: rotateZ(442deg) rotateY(360deg) translateY(-700px) scale(0.01);
        transform: rotateZ(442deg) rotateY(360deg) translateY(-700px) scale(0.01);
      }
    }

    .text.part2 span:nth-child(3) .character {
      color: #cee007;
      -webkit-animation: wave 1.2s linear infinite;
      animation: wave 1.2s linear infinite;
      -webkit-animation-delay: 3s;
      animation-delay: 3s;
    }

    .text.part2 span:nth-child(3) span::before {
      -webkit-animation: shadow 1.2s linear infinite;
      animation: shadow 1.2s linear infinite;
      -webkit-animation-delay: 3s;
      animation-delay: 3s;
    }

    .text.part2 span:nth-child(4).poofed {
      -webkit-animation: sec_poofing4 1.4s ease-in-out infinite alternate;
      animation: sec_poofing4 1.4s ease-in-out infinite alternate;
    }

    @-webkit-keyframes sec_poofing4 {
      0% {
        -webkit-transform: rotateZ(0deg) rotateY(0) translateY(0px) scale(1);
        transform: rotateZ(0deg) rotateY(0) translateY(0px) scale(1);
      }
      50% {
        -webkit-transform: rotateZ(0deg) rotateY(360deg) translateY(0px) scale(1);
        transform: rotateZ(0deg) rotateY(360deg) translateY(0px) scale(1);
      }
      56% {
        -webkit-transform: rotateZ(525deg) rotateY(360deg) translateY(0px) scale(1);
        transform: rotateZ(525deg) rotateY(360deg) translateY(0px) scale(1);
      }
      100% {
        -webkit-transform: rotateZ(525deg) rotateY(360deg) translateY(-700px) scale(0.01);
        transform: rotateZ(525deg) rotateY(360deg) translateY(-700px) scale(0.01);
      }
    }

    @keyframes sec_poofing4 {
      0% {
        -webkit-transform: rotateZ(0deg) rotateY(0) translateY(0px) scale(1);
        transform: rotateZ(0deg) rotateY(0) translateY(0px) scale(1);
      }
      50% {
        -webkit-transform: rotateZ(0deg) rotateY(360deg) translateY(0px) scale(1);
        transform: rotateZ(0deg) rotateY(360deg) translateY(0px) scale(1);
      }
      56% {
        -webkit-transform: rotateZ(525deg) rotateY(360deg) translateY(0px) scale(1);
        transform: rotateZ(525deg) rotateY(360deg) translateY(0px) scale(1);
      }
      100% {
        -webkit-transform: rotateZ(525deg) rotateY(360deg) translateY(-700px) scale(0.01);
        transform: rotateZ(525deg) rotateY(360deg) translateY(-700px) scale(0.01);
      }
    }

    .text.part2 span:nth-child(4) .character {
      color: #00c6b2;
      -webkit-animation: wave 1.2s linear infinite;
      animation: wave 1.2s linear infinite;
      -webkit-animation-delay: 3.33333s;
      animation-delay: 3.33333s;
    }

    .text.part2 span:nth-child(4) span::before {
      -webkit-animation: shadow 1.2s linear infinite;
      animation: shadow 1.2s linear infinite;
      -webkit-animation-delay: 3.33333s;
      animation-delay: 3.33333s;
    }

    .text.part2 span:nth-child(5).poofed {
      -webkit-animation: sec_poofing5 1.4s ease-in-out infinite alternate;
      animation: sec_poofing5 1.4s ease-in-out infinite alternate;
    }

    @-webkit-keyframes sec_poofing5 {
      0% {
        -webkit-transform: rotateZ(0deg) rotateY(0) translateY(0px) scale(1);
        transform: rotateZ(0deg) rotateY(0) translateY(0px) scale(1);
      }
      50% {
        -webkit-transform: rotateZ(0deg) rotateY(360deg) translateY(0px) scale(1);
        transform: rotateZ(0deg) rotateY(360deg) translateY(0px) scale(1);
      }
      56% {
        -webkit-transform: rotateZ(419deg) rotateY(360deg) translateY(0px) scale(1);
        transform: rotateZ(419deg) rotateY(360deg) translateY(0px) scale(1);
      }
      100% {
        -webkit-transform: rotateZ(419deg) rotateY(360deg) translateY(-700px) scale(0.01);
        transform: rotateZ(419deg) rotateY(360deg) translateY(-700px) scale(0.01);
      }
    }

    @keyframes sec_poofing5 {
      0% {
        -webkit-transform: rotateZ(0deg) rotateY(0) translateY(0px) scale(1);
        transform: rotateZ(0deg) rotateY(0) translateY(0px) scale(1);
      }
      50% {
        -webkit-transform: rotateZ(0deg) rotateY(360deg) translateY(0px) scale(1);
        transform: rotateZ(0deg) rotateY(360deg) translateY(0px) scale(1);
      }
      56% {
        -webkit-transform: rotateZ(419deg) rotateY(360deg) translateY(0px) scale(1);
        transform: rotateZ(419deg) rotateY(360deg) translateY(0px) scale(1);
      }
      100% {
        -webkit-transform: rotateZ(419deg) rotateY(360deg) translateY(-700px) scale(0.01);
        transform: rotateZ(419deg) rotateY(360deg) translateY(-700px) scale(0.01);
      }
    }

    .text.part2 span:nth-child(5) .character {
      color: #4e2a84;
      -webkit-animation: wave 1.2s linear infinite;
      animation: wave 1.2s linear infinite;
      -webkit-animation-delay: 3.66667s;
      animation-delay: 3.66667s;
    }

    .text.part2 span:nth-child(5) span::before {
      -webkit-animation: shadow 1.2s linear infinite;
      animation: shadow 1.2s linear infinite;
      -webkit-animation-delay: 3.66667s;
      animation-delay: 3.66667s;
    }

    .text.part2 span:nth-child(6).poofed {
      -webkit-animation: sec_poofing6 1.4s ease-in-out infinite alternate;
      animation: sec_poofing6 1.4s ease-in-out infinite alternate;
    }

    @-webkit-keyframes sec_poofing6 {
      0% {
        -webkit-transform: rotateZ(0deg) rotateY(0) translateY(0px) scale(1);
        transform: rotateZ(0deg) rotateY(0) translateY(0px) scale(1);
      }
      50% {
        -webkit-transform: rotateZ(0deg) rotateY(360deg) translateY(0px) scale(1);
        transform: rotateZ(0deg) rotateY(360deg) translateY(0px) scale(1);
      }
      56% {
        -webkit-transform: rotateZ(246deg) rotateY(360deg) translateY(0px) scale(1);
        transform: rotateZ(246deg) rotateY(360deg) translateY(0px) scale(1);
      }
      100% {
        -webkit-transform: rotateZ(246deg) rotateY(360deg) translateY(-700px) scale(0.01);
        transform: rotateZ(246deg) rotateY(360deg) translateY(-700px) scale(0.01);
      }
    }

    @keyframes sec_poofing6 {
      0% {
        -webkit-transform: rotateZ(0deg) rotateY(0) translateY(0px) scale(1);
        transform: rotateZ(0deg) rotateY(0) translateY(0px) scale(1);
      }
      50% {
        -webkit-transform: rotateZ(0deg) rotateY(360deg) translateY(0px) scale(1);
        transform: rotateZ(0deg) rotateY(360deg) translateY(0px) scale(1);
      }
      56% {
        -webkit-transform: rotateZ(246deg) rotateY(360deg) translateY(0px) scale(1);
        transform: rotateZ(246deg) rotateY(360deg) translateY(0px) scale(1);
      }
      100% {
        -webkit-transform: rotateZ(246deg) rotateY(360deg) translateY(-700px) scale(0.01);
        transform: rotateZ(246deg) rotateY(360deg) translateY(-700px) scale(0.01);
      }
    }

    .text.part2 span:nth-child(6) .character {
      color: #ff5a5f;
      -webkit-animation: wave 1.2s linear infinite;
      animation: wave 1.2s linear infinite;
      -webkit-animation-delay: 4s;
      animation-delay: 4s;
    }

    .text.part2 span:nth-child(6) span::before {
      -webkit-animation: shadow 1.2s linear infinite;
      animation: shadow 1.2s linear infinite;
      -webkit-animation-delay: 4s;
      animation-delay: 4s;
    }

    .text.part2 span:nth-child(7).poofed {
      -webkit-animation: sec_poofing7 1.4s ease-in-out infinite alternate;
      animation: sec_poofing7 1.4s ease-in-out infinite alternate;
    }

    @-webkit-keyframes sec_poofing7 {
      0% {
        -webkit-transform: rotateZ(0deg) rotateY(0) translateY(0px) scale(1);
        transform: rotateZ(0deg) rotateY(0) translateY(0px) scale(1);
      }
      50% {
        -webkit-transform: rotateZ(0deg) rotateY(360deg) translateY(0px) scale(1);
        transform: rotateZ(0deg) rotateY(360deg) translateY(0px) scale(1);
      }
      56% {
        -webkit-transform: rotateZ(206deg) rotateY(360deg) translateY(0px) scale(1);
        transform: rotateZ(206deg) rotateY(360deg) translateY(0px) scale(1);
      }
      100% {
        -webkit-transform: rotateZ(206deg) rotateY(360deg) translateY(-700px) scale(0.01);
        transform: rotateZ(206deg) rotateY(360deg) translateY(-700px) scale(0.01);
      }
    }

    @keyframes sec_poofing7 {
      0% {
        -webkit-transform: rotateZ(0deg) rotateY(0) translateY(0px) scale(1);
        transform: rotateZ(0deg) rotateY(0) translateY(0px) scale(1);
      }
      50% {
        -webkit-transform: rotateZ(0deg) rotateY(360deg) translateY(0px) scale(1);
        transform: rotateZ(0deg) rotateY(360deg) translateY(0px) scale(1);
      }
      56% {
        -webkit-transform: rotateZ(206deg) rotateY(360deg) translateY(0px) scale(1);
        transform: rotateZ(206deg) rotateY(360deg) translateY(0px) scale(1);
      }
      100% {
        -webkit-transform: rotateZ(206deg) rotateY(360deg) translateY(-700px) scale(0.01);
        transform: rotateZ(206deg) rotateY(360deg) translateY(-700px) scale(0.01);
      }
    }

    .text.part2 span:nth-child(7) .character {
      color: #f99b0c;
      -webkit-animation: wave 1.2s linear infinite;
      animation: wave 1.2s linear infinite;
      -webkit-animation-delay: 4.33333s;
      animation-delay: 4.33333s;
    }

    .text.part2 span:nth-child(7) span::before {
      -webkit-animation: shadow 1.2s linear infinite;
      animation: shadow 1.2s linear infinite;
      -webkit-animation-delay: 4.33333s;
      animation-delay: 4.33333s;
    }

    .text.part2 span:nth-child(8).poofed {
      -webkit-animation: sec_poofing8 1.4s ease-in-out infinite alternate;
      animation: sec_poofing8 1.4s ease-in-out infinite alternate;
    }

    @-webkit-keyframes sec_poofing8 {
      0% {
        -webkit-transform: rotateZ(0deg) rotateY(0) translateY(0px) scale(1);
        transform: rotateZ(0deg) rotateY(0) translateY(0px) scale(1);
      }
      50% {
        -webkit-transform: rotateZ(0deg) rotateY(360deg) translateY(0px) scale(1);
        transform: rotateZ(0deg) rotateY(360deg) translateY(0px) scale(1);
      }
      56% {
        -webkit-transform: rotateZ(60deg) rotateY(360deg) translateY(0px) scale(1);
        transform: rotateZ(60deg) rotateY(360deg) translateY(0px) scale(1);
      }
      100% {
        -webkit-transform: rotateZ(60deg) rotateY(360deg) translateY(-700px) scale(0.01);
        transform: rotateZ(60deg) rotateY(360deg) translateY(-700px) scale(0.01);
      }
    }

    @keyframes sec_poofing8 {
      0% {
        -webkit-transform: rotateZ(0deg) rotateY(0) translateY(0px) scale(1);
        transform: rotateZ(0deg) rotateY(0) translateY(0px) scale(1);
      }
      50% {
        -webkit-transform: rotateZ(0deg) rotateY(360deg) translateY(0px) scale(1);
        transform: rotateZ(0deg) rotateY(360deg) translateY(0px) scale(1);
      }
      56% {
        -webkit-transform: rotateZ(60deg) rotateY(360deg) translateY(0px) scale(1);
        transform: rotateZ(60deg) rotateY(360deg) translateY(0px) scale(1);
      }
      100% {
        -webkit-transform: rotateZ(60deg) rotateY(360deg) translateY(-700px) scale(0.01);
        transform: rotateZ(60deg) rotateY(360deg) translateY(-700px) scale(0.01);
      }
    }

    .text.part2 span:nth-child(8) .character {
      color: #cee007;
      -webkit-animation: wave 1.2s linear infinite;
      animation: wave 1.2s linear infinite;
      -webkit-animation-delay: 4.66667s;
      animation-delay: 4.66667s;
    }

    .text.part2 span:nth-child(8) span::before {
      -webkit-animation: shadow 1.2s linear infinite;
      animation: shadow 1.2s linear infinite;
      -webkit-animation-delay: 4.66667s;
      animation-delay: 4.66667s;
    }

    .text.part2 span:nth-child(9).poofed {
      -webkit-animation: sec_poofing9 1.4s ease-in-out infinite alternate;
      animation: sec_poofing9 1.4s ease-in-out infinite alternate;
    }

    @-webkit-keyframes sec_poofing9 {
      0% {
        -webkit-transform: rotateZ(0deg) rotateY(0) translateY(0px) scale(1);
        transform: rotateZ(0deg) rotateY(0) translateY(0px) scale(1);
      }
      50% {
        -webkit-transform: rotateZ(0deg) rotateY(360deg) translateY(0px) scale(1);
        transform: rotateZ(0deg) rotateY(360deg) translateY(0px) scale(1);
      }
      56% {
        -webkit-transform: rotateZ(496deg) rotateY(360deg) translateY(0px) scale(1);
        transform: rotateZ(496deg) rotateY(360deg) translateY(0px) scale(1);
      }
      100% {
        -webkit-transform: rotateZ(496deg) rotateY(360deg) translateY(-700px) scale(0.01);
        transform: rotateZ(496deg) rotateY(360deg) translateY(-700px) scale(0.01);
      }
    }

    @keyframes sec_poofing9 {
      0% {
        -webkit-transform: rotateZ(0deg) rotateY(0) translateY(0px) scale(1);
        transform: rotateZ(0deg) rotateY(0) translateY(0px) scale(1);
      }
      50% {
        -webkit-transform: rotateZ(0deg) rotateY(360deg) translateY(0px) scale(1);
        transform: rotateZ(0deg) rotateY(360deg) translateY(0px) scale(1);
      }
      56% {
        -webkit-transform: rotateZ(496deg) rotateY(360deg) translateY(0px) scale(1);
        transform: rotateZ(496deg) rotateY(360deg) translateY(0px) scale(1);
      }
      100% {
        -webkit-transform: rotateZ(496deg) rotateY(360deg) translateY(-700px) scale(0.01);
        transform: rotateZ(496deg) rotateY(360deg) translateY(-700px) scale(0.01);
      }
    }

    .text.part2 span:nth-child(9) .character {
      color: #00c6b2;
      -webkit-animation: wave 1.2s linear infinite;
      animation: wave 1.2s linear infinite;
      -webkit-animation-delay: 5s;
      animation-delay: 5s;
    }

    .text.part2 span:nth-child(9) span::before {
      -webkit-animation: shadow 1.2s linear infinite;
      animation: shadow 1.2s linear infinite;
      -webkit-animation-delay: 5s;
      animation-delay: 5s;
    }

    @-webkit-keyframes wave {
      0% {
        -webkit-transform: translateY(0);
        transform: translateY(0);
      }
      50% {
        -webkit-transform: translateY(-10px);
        transform: translateY(-10px);
      }
      100% {
        -webkit-transform: translateY(0);
        transform: translateY(0);
      }
    }

    @keyframes wave {
      0% {
        -webkit-transform: translateY(0);
        transform: translateY(0);
      }
      50% {
        -webkit-transform: translateY(-10px);
        transform: translateY(-10px);
      }
      100% {
        -webkit-transform: translateY(0);
        transform: translateY(0);
      }
    }

    @-webkit-keyframes shadow {
      0% {
        width: 0;
        height: 0;
      }
      50% {
        width: 100%;
        height: 100%;
      }
      100% {
        width: 0;
        height: 0;
      }
    }

    @keyframes shadow {
      0% {
        width: 0;
        height: 0;
      }
      50% {
        width: 100%;
        height: 100%;
      }
      100% {
        width: 0;
        height: 0;
      }
    }
  }
</style>
