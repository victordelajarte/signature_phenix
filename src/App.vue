<template>
  <div id="app">
    <div class="content">
      <div class="left">
        <h1
          style="text-align:center"
        >Phenix email signature generator</h1>
        <div class="flags">
          <button class="countryButton" v-on:click="updateLocale('fr_fr')"><span class="emoji">🇫🇷</span></button>
          <button class="countryButton" v-on:click="updateLocale('es_es')"><span class="emoji">🇪🇸</span></button>
          <button class="countryButton" v-on:click="updateLocale('pt_pt')"><span class="emoji">🇵🇹</span></button>
          <button class="countryButton" v-on:click="updateLocale('en_gb')"><span class="emoji">🇬🇧</span></button>
        </div>

        <label>
          {{locale.text.firstName}} :
          <input type="text" v-model="firstName" required @blur="remplirMail">
        </label>
        <label>
          {{locale.text.lastName}} :
          <input type="text" v-model="lastName" required>
        </label>
        <label>
          {{locale.text.job}} :
          <input type="text" v-model="job" required>
        </label>
        <label>
          {{locale.text.officeAddress}} :
          <input type="text" v-model="officeAddr" required>
        </label>
        <label>
          {{locale.text.additionnalOfficeAddress}}* :
          <input type="text" v-model="additionnalAddr">
        </label>
        <label>
          {{locale.text.emailPrefix}} :
          <input type="text" v-model="prefixeMail" required>
        </label>
        <span class="email">@wearephenix.com</span>
        <label>
          {{locale.text.phoneNumber}} : {{locale.phonePrefix}}
          <input
            type="text"
            v-model="numeroTelephone"
            required
            pattern="\d{9}"
            maxlength="9"
            @keydown="checkIfNumber"
          >
        </label>
        <p
          style="font-size:12px"
        >* {{locale.text.footNote1}}</p>
      </div>
      <div class="right">
        <div id="result">
          <a :href="locale.websiteUrl" target="_blank" style="text-decoration:none;">
            <img
              :src="`${publicPath}assets/V2/logo.png`"
              style="display:block; margin-bottom: 10px"
              alt="logo phenix"
            >
          </a>
          <div>
            <p
              style="font-size:14px; line-height: 1.2; color: #289CDB; letter-spacing: 0.4px; font-family: Trebuchet, Arial, Helvetica, sans-serif; font-weight:700; margin-top: 2px; margin-bottom:5px; text-transform:capitalize"
            >
              {{nameFormatted}}
              <span
                style="font-size:14px; line-height: 1.2; color:#289CDB; font-family: Trebuchet, Arial, Helvetica, sans-serif; font-weight:400;text-transform:none"
              >&nbsp;-&nbsp;{{job}}</span>
            </p>
            <p
              style="font-size:11px; line-height: 1.2; color:#aaaaaa; letter-spacing: 0.4px; font-family: Trebuchet, Arial, Helvetica, sans-serif; margin-top: 2px; margin-bottom:2px"
            >{{officeAddr}}</p>
            <p
              style="font-size:11px; line-height: 1.2; color:#aaaaaa; letter-spacing: 0.4px; font-family: Trebuchet, Arial, Helvetica, sans-serif; margin-top: 2px; margin-bottom:2px"
            >{{additionnalAddr}}</p>
            <a
              :href="locale.websiteUrl"
              style="line-height: 1.2; font-family: Trebuchet, Arial, Helvetica, sans-serif; font-size:11px; color:#aaaaaa;display:block;margin-top: 2px; margin-bottom: 5px;"
              target="_blank"
            >{{locale.websiteUrl}}</a>
            <img
              :src="`${publicPath}assets/V2/mail.png`"
              alt="mail"
              style="vertical-align: sub; margin-right: 15px; display: inline-block; margin-top: 5px"
              width="18"
              height="18"
            >
            <a
              :href="`mailto:${completeEmail}`"
              style="font-size:11px; line-height: 1.2; display:inline-block; color:#aaaaaa; letter-spacing: 0.4px; font-family: Trebuchet, Arial, Helvetica, sans-serif; margin-top: 2px; margin-bottom:5px; text-decoration:underline;text-transform:lowercase;vertical-align: bottom;"
              target="_blank"
            >{{completeEmail}}</a>
            <br>
            <img
              :src="`${publicPath}assets/V2/phone.png`"
              alt="tel"
              style="vertical-align: sub; margin-right: 15px; display: inline-block; margin-top: 5px"
            >
            <span
              style="font-size:11px; line-height: 1.2; color:#aaaaaa; letter-spacing: 0.4px; font-family: Trebuchet, Arial, Helvetica, sans-serif; margin-top: 2px; margin-bottom:5px; display: inline-block; vertical-align: bottom"
            >{{locale.phonePrefix}}{{numeroTelephoneFormatted}}</span>
            <p style="margin-top: 8px; margin-left: -3px; line-height: 1.2;">
              <a
                v-if="locale.hasOwnProperty('facebookLink')"
                :href="locale.facebookLink"
                target="_blank"
                style="text-decoration:none;"
              >
                <img
                  :src="`${publicPath}assets/icon-facebook-30.png`"
                  style="padding-right:5px; border:none;"
                  alt="fb"
                >
              </a>
              <a v-if="locale.hasOwnProperty('twitterLink')" :href="locale.twitterLink" target="_blank" style="text-decoration:none;">
                <img
                  :src="`${publicPath}assets/icon-twitter-30.png`"
                  style="padding-right:5px; border:none;"
                  alt="tw"
                >
              </a>
              <a
                v-if="locale.hasOwnProperty('instagramLink')"
                :href="locale.instagramLink"
                target="_blank"
                style="text-decoration:none;"
              >
                <img
                  :src="`${publicPath}assets/icon-insta-30.png`"
                  style="padding-right:5px; border:none;"
                  alt="ig"
                >
              </a>
              <a
                v-if="locale.hasOwnProperty('linkedinLink')"
                :href="locale.linkedinLink"
                target="_blank"
                style="text-decoration:none;"
              >
                <img
                  :src="`${publicPath}assets/icon-linkedin-30.png`"
                  style="padding-right:5px; border:none;"
                  alt="linkedin"
                >
              </a>
            </p>
          </div>
        </div>
        <button class="bouton" @click="copySignature" :disabled="!canCopy">{{locale.text.copySignature}}</button>
      </div>
    </div>
  </div>
</template>

<script>
import {locales} from './locales.js'
export default {
  name: "app",
  data() {
    return {
      publicPath: process.env.BASE_URL,
      firstName: "",
      lastName: "",
      job: "",
      officeAddr: "",
      additionnalAddr: "",
      prefixeMail: "",
      numeroTelephone: "",
      locale: locales['fr_fr']
    };
  },
  computed: {
    isNumeroTelValide: function() {
      // On ne veut que les inputs à 9 caractères et entièrement constitués de nombres
      // const isRightLength = this.numeroTelephone.length == 9;
      // const isOnlyNumbers = /^\d+$/.test(this.numeroTelephone);
      return (
        this.numeroTelephone.length == 9 && /^\d+$/.test(this.numeroTelephone)
      );
    },
    canCopy: function() {
      return (
        this.firstName.trim().length > 0 &&
        this.lastName.trim().length > 0 &&
        this.job.trim().length > 0 &&
        this.officeAddr.trim().length > 0 &&
        this.prefixeMail.trim().length > 0 &&
        this.isNumeroTelValide
      );
    },
    numeroTelephoneFormatted: function() {
      // Possibilité de valider/formatter le numéro de téléphone en live si besoin
      if (this.numeroTelephone.length > 1) {
        let result = this.numeroTelephone[0];
        for (let i = 1; i < this.numeroTelephone.length; i += 2) {
          result += " " + this.numeroTelephone[i];
          if (this.numeroTelephone[i + 1] != undefined) {
            result += this.numeroTelephone[i + 1];
          }
        }
        return result;
      } else {
        return this.numeroTelephone;
      }
    },
    nameFormatted: function() {
      return this.firstName.toLowerCase() + " " + this.lastName.toLowerCase();
    },
    completeEmail: function() {
      return this.prefixeMail + "@wearephenix.com";
    }
  },
  methods: {
    updateLocale: function(code) {
      this.locale = locales[code]
    },
    checkIfNumber: function(e) {
      // console.log(e);
      // console.log(e.key);
      // si la key ne fait qu'un caractère et que ce n'est pas un chiffre, ça ne passe pas
      if (e.key.length == 1 && !/\d/.test(e.key))
        e.preventDefault() && e.stopPropagation();
    },
    remplirMail: function() {
      if (!this.prefixeMail && this.firstName)
        this.prefixeMail = this.firstName.toLowerCase();
    },
    copySignature: function() {
      if (!this.canCopy) return;
      const resultInput = document.getElementById("result");

      if (document.selection) {
        const range = document.body.createTextRange();
        range.moveToElementText(resultInput);
        range.select();
      } else if (window.getSelection()) {
        const range = document.createRange();
        range.selectNode(resultInput);
        window.getSelection().removeAllRanges();
        window.getSelection().addRange(range);
      }

      try {
        if (document.execCommand("copy")) alert("Signature copiée !");
        else
          alert(
            "Erreur, merci de réessayer ou de demander à nos supers développeurs de l'aide !"
          );
      } catch (err) {
        alert(
          "Erreur, merci de réessayer ou de demander à nos supers développeurs de l'aide !"
        );
      }
    }
  }
};
</script>

<style>
* {
  margin: 0;
  padding: 0;
  box-sizing: border-box;
}
#app {
  display: flex;
  justify-content: center;
  padding: 10vh 0;
  height: 100vh;
  width: 100vw;
}
.content {
  width: 90vw;
  display: flex;
  position: relative;
  font-size: 13px;
  border: 1px solid #009cdf;
  padding: 50px 0;
  border-radius: 5px;
  font-family: Trebuchet, Arial, Helvetica, sans-serif;
}

@media screen and (max-width: 768px) {
  .content {
    flex-direction: column;
  }
}

.left,
.right {
  margin: 2em;
  flex: 1;
}

.left {
  /* background: red; */
}

.right {
  display: flex;
  flex-direction: column;
}

label {
  display: flex;
  margin-bottom: 0.5em;
  position: relative;
  height: 3rem;
  font-size: 1.5em;
  align-items: center;
}

input {
  text-decoration: none;
  margin: 0 2em;
  flex: 1;
  height: 100%;
  padding: 0 0.7em;
}

.email {
  position: relative;
  left: calc(100% - 20ch);
  top: -8px;
}

.bouton {
  background-color: #289cdb;
  border: none;
  padding: 10px;
  color: #fff;
  margin-top: 12px;
  cursor: pointer;
}

.countryButton {
  background-color: #289cdb00;
  border: none;
  padding: 10px;
  margin: 12px;
  cursor: pointer;
}

.flags {
  display:flex;
  justify-content:center;
  align-items:center;
}

span.emoji {
  font-size: 40px;
  vertical-align: middle;
  line-height: 2;
}

.bouton:disabled {
  background: grey;
  cursor: not-allowed;
}

.copyright {
  font-size: 10px;
  position: fixed;
  bottom: 20px;
}
</style>
