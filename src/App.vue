<template>
  <div id="app">
    <div class="content">
      <div class="left">
        <h1
          style="text-align:center"
        >Création de signatures automatiques pour la messagerie Gmail.com</h1>
        <br>
        <label>
          Votre prénom :
          <input type="text" v-model="firstName" required @blur="remplirMail">
        </label>
        <label>
          Votre nom :
          <input type="text" v-model="lastName" required>
        </label>
        <label>
          Votre fonction :
          <input type="text" v-model="fonction" required>
        </label>
        <label>
          Adresse du bureau :
          <input type="text" v-model="adresseEtablissement" required>
        </label>
        <label>
          Adresse supplémentaire* :
          <input type="text" v-model="adresseSupplementaire">
        </label>
        <label>
          Préfixe de votre adresse mail :
          <input type="text" v-model="prefixeMail" required>
        </label>
        <span class="email">@wearephenix.com</span>
        <label>
          Votre numéro de téléphone : +33(0)
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
        >* Pour les personnes qui disposent de deux adresses postales différentes</p>
      </div>
      <div class="right">
        <div id="result">
          <link
            href="https://fonts.googleapis.com/css?family=Montserrat:400,700&display=swap"
            rel="stylesheet"
          >
          <div style="display:inline-block; float:left">
            <a href="https://www.wearephenix.com" target="_blank" style="text-decoration:none;">
              <img
                :src="`${publicPath}assets/phenix_ecrit.png`"
                style="display:block; margin-right:17px;width:75px;margin-top:25px"
                alt="logo phenix"
              >
            </a>
          </div>
          <div
            style="text-align:left; display:inline-block; border-left:3px solid #009CDF; padding-left:7px;"
          >
            <p
              style="font-size:14px; color:#009CDF; font-family: 'Montserrat', Arial, Helvetica, sans-serif; font-weight:700; margin-top: 2px; margin-bottom:5px; text-transform:capitalize"
            >
              {{firstName}}
              <span
                style="font-size:14px; color:#009CDF; font-family: 'Montserrat', Arial, Helvetica, sans-serif; font-weight:700; margin-top: 2px; text-transform: uppercase; margin-bottom:5px;"
              >{{lastName}}</span>
              <span
                style="font-size:14px; color:#333333; font-family: 'Montserrat', Arial, Helvetica, sans-serif; font-weight:400;text-transform:none"
              >&nbsp;-&nbsp;{{fonction}}</span>
            </p>
            <p
              style="font-size:11px; color:#aaaaaa; font-family: 'Montserrat', Arial, Helvetica, sans-serif; margin-top: 2px; margin-bottom:2px; text-decoration:none;"
            >{{adresseEtablissement}}</p>
            <p
              style="font-size:11px; color:#aaaaaa; font-family: 'Montserrat', Arial, Helvetica, sans-serif; margin-top: 2px; margin-bottom:2px; text-decoration:none;"
            >{{adresseSupplementaire}}</p>
            <p style="margin-top: 2px; margin-bottom:5px;">
              <a
                href="http://www.wearephenix.com"
                style="text-decoration:none; font-family: 'Montserrat', Arial, Helvetica, sans-serif; font-size:11px; color:#aaaaaa; "
                target="_blank"
              >www.wearephenix.com</a>
            </p>
            <p
              style="font-size:11px; color:#aaaaaa; font-family: 'Montserrat', Arial, Helvetica, sans-serif; margin-top: 2px; margin-bottom:5px; text-decoration:none;text-transform:lowercase"
            >
              <img
                :src="`${publicPath}assets/email_bleu.png`"
                alt="mail"
                style="vertical-align: sub;"
              >
              {{prefixeMail}}@wearephenix.com
            </p>
            <p
              style="font-size:11px; color:#aaaaaa; font-family: 'Montserrat', Arial, Helvetica, sans-serif; margin-top: 2px; margin-bottom:5px;"
            >
              <img :src="`${publicPath}assets/tel_bleu.png`" alt="tel" style="vertical-align: sub;">
              +33(0){{numeroTelephoneFormatted}}
            </p>
            <p style="margin-bottom:5px;">
              <a
                href="https://www.facebook.com/wearephenix"
                target="_blank"
                style="text-decoration:none;"
              >
                <img
                  :src="`${publicPath}assets/icon-facebook-30.png`"
                  style="padding-right:5px; border:none;"
                  alt="fb"
                >
              </a>
              <a href="https://twitter.com/phenixfr" target="_blank" style="text-decoration:none;">
                <img
                  :src="`${publicPath}assets/icon-twitter-30.png`"
                  style="padding-right:5px; border:none;"
                  alt="tw"
                >
              </a>
              <a
                href="https://instagram.com/phenix_antigaspi"
                target="_blank"
                style="text-decoration:none;"
              >
                <img
                  :src="`${publicPath}assets/icon-insta-30.png`"
                  style="padding-right:5px; border:none;"
                  alt="tw"
                >
              </a>
            </p>
          </div>
        </div>
        <button class="bouton" @click="copySignature" :disabled="!canCopy">Copier la signature</button>
      </div>
    </div>
  </div>
</template>

<script>
export default {
  name: "app",
  data() {
    return {
      publicPath: process.env.BASE_URL,
      firstName: "",
      lastName: "",
      fonction: "",
      adresseEtablissement: "",
      adresseSupplementaire: "",
      prefixeMail: "",
      numeroTelephone: ""
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
        this.fonction.trim().length > 0 &&
        this.adresseEtablissement.trim().length > 0 &&
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
    }
  },
  methods: {
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
/* @font-face {
  font-family: Circular;
  src: url("../public/assets/CircularStd-Medium.otf") format("opentype");
} */

* {
  margin: 0;
  padding: 0;
  box-sizing: border-box;
}
#app {
  display: flex;
  justify-content: center;
  padding: 15vh 0;
  height: 100vh;
  width: 100vw;
}
.content {
  width: 80vw;
  display: flex;
  position: relative;
  font-size: 13px;
  border: 1px solid #009cdf;
  padding: 50px 0;
  border-radius: 5px;
  font-family: "Montserrat", Arial, Helvetica, sans-serif;
}

.left,
.right {
  margin: 0 5%;
}

.left {
  /* font-family: Circular; */
  width: 40%;
}

.right {
  display: flex;
  flex-direction: column;
  margin: 2em auto;
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
  background-color: #009cdf;
  border: none;
  padding: 10px;
  color: #fff;
  margin-right: -26px;
  margin-top: 12px;
  cursor: pointer;
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
