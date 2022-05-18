<template>
  <v-layout row wrap align-center class="samim-bg">
    <v-flex
      sm12
      md4
      offset-md4
      class="d-flex flex-column align-center justify-center"
    >
      <v-card
        class="login-card"
        style="box-shadow: none !important"
        elevation="4"
        light
        color="transparent"
      >
        <v-card-title>
          <v-layout>
            <v-flex class="d-flex flex-column align-center justify-center">
              <v-img
                :alt="platformName"
                contain
                src="@/assets/logo.png"
                style="max-width: 200px"
              ></v-img>
            </v-flex>
          </v-layout>
        </v-card-title>
        <v-divider></v-divider>
        <v-card-text class="pb-0">
          <p v-if="loginLevel === 1" style="color: white; font-size: 10px">
            برای ورود، شماره تلفنی که با آن ثبت نام کرده اید را وارد کنید
          </p>
          <p v-if="loginLevel === 2" style="color: white; font-size: 10px">
            {{
              "  لطفا کد 4 رقمی ارسال شده به شماره" +
              phoneNumber +
              "  را وارد نمایید"
            }}
          </p>
          <v-form 
            ref="form1" 
            v-model="valid" 
            lazy-validation
            @submit="submit"
            onSubmit="return false"
          >
            <v-text-field
              v-if="loginLevel === 1"
              outlined
              color="white"
              dark
              label="شماره موبایل"
              type="text"
              :rules="formRules.mobileNumber"
              @keyup.enter="handleEnter()"
              required
              v-model="mobileNumber"
              append-icon="mdi-cellphone"
              hide-details
              class="mb-3"
            ></v-text-field>
            <v-text-field
              v-if="loginLevel !== 1 "
              outlined
              color="white"
              dark
              label="کد ارسال شده"
              v-model="code"
              :rules="formRules.code"
              @keyup.enter="handleEnter()"
              required
              hide-details
              class="mb-3"
            >
            </v-text-field>
            
          </v-form>
        </v-card-text>
        <v-card-actions
          class="mb-2 d-flex flex-column justify-center align-center"
        >
          <v-btn
            v-if="loginLevel === 1"
            class="confirm-button"
            color="#DFB456"
            :loading="sendingPhoneNumber"
            :disabled="sendingPhoneNumber"
            @click="sendNumber"
          >
            <v-icon color="#1E3E74" left>mdi-lock</v-icon>
            ورود به سیستم
          </v-btn>
          <v-row v-if="loginLevel !== 1" class="d-flex ma-0 full-width justify-space-around flex-row">
            <v-btn
              class="confirm-button"
              color="#DFB456"
              :loading="verifyingPhoneNumber"
              @click="verifyNumber"
            >
              <v-icon color="#1E3E74" left>mdi-arrow-up-box</v-icon>
              ارسال
            </v-btn>
            <v-btn
              color="#DFB456"
              :loading="sendingPhoneNumber"
              :disabled="!resend"
              @click="resendNumber"
              text
            >
              ارسال مجدد کد 
            </v-btn>
          </v-row>
          
          <div
            v-if="loginLevel === 2 && !resend"
            class="d-flex flex-row align-center mt-2"
          >
            <p
              class="ma-0 pa-0"
              v-if="loginLevel === 2"
              style="color: white; font-size: 10px"
            >
              زمان باقیمانده برای ارسال مجدد
            </p>
            <p
              class="mr-2 ma-0 pa-0 d-flex justify-center align-center"
              style="
                width: 28px;
                height: 28px;
                color: white;
                font-size: 12px;
                border-radius: 28px;
                border: 1px solid white;
              "
            >
              {{ codeTimer }}
            </p>
          </div>
        </v-card-actions>
      </v-card>

      <v-flex>
        <v-layout align-center justify-space-between>
          <p class="caption my-3" style="color: #dfb456; font-size: 10px">
            طراحی و توسعه توسط
            <a
              style="color: #dfb456; font-size: 12px"
              href="http://pbit-co.com/"
              >پیشگام بینش</a
            >
          </p>
          <p class="caption my-3" style="color: #dfb456; font-size: 10px">
            Samim Portal v1
          </p>
        </v-layout>
      </v-flex>
    </v-flex>
  </v-layout>
</template>


<style>
.samim-bg {
  height: 100%;
  margin-right: 0px;
  margin-left: 0px;
  margin-top: 0px;
  margin-bottom: 0px;
  margin: 0px !important;
  overflow: hidden;
  background-color: #1e3e74;
}

.absolute {
  max-width: 100% !important;
  padding: 0px !important;
}

.login-card > input[type="text"] {
  background-color: #1e3e74 !important;
}

.login-card {
  box-shadow: none !important;
}

.confirm-button .--disabled {
  background-color: #dfb456;
}
</style>

<script>
import { mapActions, mapGetters } from "vuex";

export default {
  name: "Login",

  data() {
    return {
      showPassword: false,
      loggingIn: false,
      valid: true,
      platformName:
        "سامانه صدور و مدیریت قبوض و تسهیلات رفاهی و پرداخت های الکترونیکی - TIS",
      code: null,
      mobileNumber: null,
      formRules: {
        code: [
          (v) => (v && v.length == 4) || "کد را به درستی وارد کنید",
          (v) => !!v || "لطفا کد ارسال شده را وارد کنید",
        ],
        mobileNumber: [(v) => !!v || "شماره موبایل را وارد کنید"],
      },
      invalidUserButton: false,
      forgotPasswordButton: false,
      invalidUserPhoneNumber: false,
      forgotPassword: false,
      codeTimer: 120,
      interval: null,
      resend: false
    };
  },

  created() {
    this.setLoginLevel(1)
  },

  computed: {
    ...mapGetters("auth", {
      phoneNumber: "phoneNumber",
      loginLevel: "loginLevel",
      sendingPhoneNumber: "sendingPhoneNumber",
      verifyingPhoneNumber: "verifyingPhoneNumber",
      isAuthenticated: "isAuthenticated"
    }),
  },

  // watch: {
  //   sendingPhoneNumber: function (newValue, old) {
  //     console.log("sendingPhoneNumber changed:", newValue);
  //   },
  //   loginLevel: function (newValue, old) {
  //     if (newValue == 2) {
  //       this.setCodeTimer();
  //     }
  //   },
  //   codeTimer: function (newValue, old) {
  //     if (old === 1 && newValue === 0) {
  //       this.unsetCodeTimer();
  //       this.code = null
  //       this.mobileNumber = null
  //       this.resend = true
  //     }
  //   },
  //   isAuthenticated: function(newValue, old) {
  //     if(newValue) {
  //       this.setLoginLevel(1)
  //       this.mobileNumber = null
  //       this.code = null
  //       this.$router.push({ path: "/" });
  //     }
  //   }
  // },

  methods: {
    handleEnter(){
      console.log("handling enter")
      if(this.loginLevel == 1){
        this.sendNumber()
      }else if(this.loginLevel == 2) {
        this.verifyNumber()
      }
    },
    setCodeTimer() {
      this.interval = setInterval(() => {
        this.codeTimer = this.codeTimer - 1;
      }, 1000);
    },
    unsetCodeTimer() {
      clearInterval(this.interval);
    },
    showSendPhoneNumberButton() {
      this.sendPhoneNumberButton = true;
      this.forgotPasswordButton = false;
    },
    sendNumber() {
      this.sendPhoneNumber({ mobile: this.mobileNumber });
    },
    resendNumber() {
      this.sendPhoneNumber({ mobile: this.phoneNumber });  
      this.codeTimer = 120
      this.resend = false
      this.setCodeTimer()
    },
    verifyNumber() {
      this.verifyCode({ 
        mobile: this.phoneNumber,
        code: this.code
      });
    },
    fixNumbers(str) {
      let persianNumbers = ["٠", "١", "٢", "٣", "٤", "٥", "٦", "٧", "٨", "٩"];
      str = str.toString();
      let length = str.length;
      for (var i = 0; i < length; i++) {
        if (str[i].match(/\d+/)) {
          str = str.replace(persianNumbers[Number(str[i])].toString(), str[i]);
        }
      }
      return str;
    },
    // loginUser: function (event) {
    //   this.loggingIn = true;
    //   this.doLoginUser({
    //     username: this.username,
    //     password: this.password,
    //   })
    //     .then((data) => {
    //       let najva_user_Token = localStorage.getItem("najva_user_Token");
    //       console.log("najva_user_Token is:", najva_user_Token);
    //       if (najva_user_Token) {
    //         this.sendNotificationToken(najva_user_Token);
    //       }
    //       this.notify({
    //         message: "خوش آمدید",
    //         status: "success",
    //       });
    //       this.loggingIn = false;
    //       this.$router.push({ path: "/" });
    //     })
    //     .catch((err) => {
    //       console.log("login error:", err);
    //       this.forgotPasswordButton = true;
    //       this.loggingIn = false;
    //     });
    // },
    ...mapActions("auth", [
      "doLoginUser",
      "sendNotificationToken",
      "verifyCode",
      "sendPhoneNumber",
      "setLoginLevel",
    ]),
    ...mapActions("apiNotify", ["notify"]),
  },
};
</script>