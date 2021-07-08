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
          <div style="caret-color: rgb(0, 0, 0); color: rgb(0, 0, 0); font-family: sans-serif; font-size: 13px; margin: 0px; padding: 0px; display: inline-block; float: none;">
            <a :href="locale.websiteUrl" target="_blank" style="text-decoration:none;">
              <!-- src/img/logo_ecrit.png -->
              <img
                src="data:image/png;base64,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"
                style="margin: 11px 7px 0px 0px; padding: 0px; display: block; width: 104px;"
                alt="logo phenix"
              >
            </a>
          </div>
          <div style="caret-color: rgb(0, 0, 0); color: rgb(0, 0, 0); font-family: sans-serif; font-size: 13px; margin: 0px; padding: 0px 0px 0px 7px; display: inline-block; border-left-width: 3px; border-left-style: solid; border-left-color: rgb(0, 156, 223);">
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
            <!-- src/img/mail.png -->
            <img
              src="data:image/png;base64,iVBORw0KGgoAAAANSUhEUgAAABIAAAASCAQAAAD8x0bcAAAABGdBTUEAALGPC/xhBQAAACBjSFJNAAB6JgAAgIQAAPoAAACA6AAAdTAAAOpgAAA6mAAAF3CculE8AAAAAmJLR0QAAKqNIzIAAAAJcEhZcwAADdcAAA3XAUIom3gAAAAHdElNRQfjBhUJBBoiXJZwAAAA6UlEQVQoz43SsUrDUBiG4Scxk1OhY8FJkKJ29wpc3IKbg+CkS+ml6NKp4FAXJZs6dHPqKJRab6AXYBcpmBqHNAZKW/KN//9y3u9wDhUSJBWgSMuhSLBhn1kYB8mbsSepULZq8Sty7ijy7tO+e+laz6WRn9BMz1xHLZ/H4gKp6ZjrmYVCadb3oa0BieVVGtom+lJhmMu9GrjR/Bc1XRt4KaxFhmYuPBvixJkHk7JamYmuK3s40DUtF2FRF0zdymTuciRePSnOC395LCdrdLFNWULbXzBSd+p7C7GrHiTHWna2QAujSl+lUv4AGMM6sadG7YYAAAAldEVYdGRhdGU6Y3JlYXRlADIwMTktMDYtMjFUMDk6MDQ6MjYrMDA6MDCcEY+fAAAAJXRFWHRkYXRlOm1vZGlmeQAyMDE5LTA2LTIxVDA5OjA0OjI2KzAwOjAw7Uw3IwAAABl0RVh0U29mdHdhcmUAd3d3Lmlua3NjYXBlLm9yZ5vuPBoAAAAASUVORK5CYII="
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
            <!-- src/img/phone.png -->
            <img
              src="data:image/png;base64,iVBORw0KGgoAAAANSUhEUgAAABIAAAASCAQAAAD8x0bcAAAABGdBTUEAALGPC/xhBQAAACBjSFJNAAB6JgAAgIQAAPoAAACA6AAAdTAAAOpgAAA6mAAAF3CculE8AAAAAmJLR0QAAKqNIzIAAAAJcEhZcwAADdcAAA3XAUIom3gAAAAHdElNRQfjBhUJBDNg7g4cAAABMUlEQVQoz3XQuy+dARzG8c/7up4OJoO4xGJplIQTBgMhIXIag5yGpLGIiI3VX2AVllYiJlPTNyaJdpAwGE4oItJVFDGdhsH9NrjE5fWbnuGb/L7PE0S0S7uWNS5L2tsLdWo2ZsSmAXlEsVC3Kbuu/HShVeyFju3f3uffku9BBfKD+1zh5D3oRimo1hYrhNC6JpC0ao/bWOiXasVYVyNBEAsdWtONLTv6iBshiEgYNS+j0LAdP14jaSFOzUorc+abKj2voUhOL2Rd6rHhyIZ2H/11pVy/Nh/sugmeHLrUm/Rfga8qbWmw6J8OeWaCZ6pdGn23j0/q/LENvqgNHv+CFimRzAulXBPhYwOwZNpng4ofqkOp3OB5C5CQkrRtxYELZYYsvxj4acYSHerkOFdkzsIdFQdOIlSssuMAAAAldEVYdGRhdGU6Y3JlYXRlADIwMTktMDYtMjFUMDk6MDQ6NTErMDA6MDBTc7gIAAAAJXRFWHRkYXRlOm1vZGlmeQAyMDE5LTA2LTIxVDA5OjA0OjUxKzAwOjAwIi4AtAAAABl0RVh0U29mdHdhcmUAd3d3Lmlua3NjYXBlLm9yZ5vuPBoAAAAASUVORK5CYII="
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
                <!-- src/img/icon-facebook-30.png -->
                <img
                  src="data:image/png;base64,iVBORw0KGgoAAAANSUhEUgAAAB4AAAAeCAYAAAA7MK6iAAAAGXRFWHRTb2Z0d2FyZQBBZG9iZSBJbWFnZVJlYWR5ccllPAAAAdhJREFUeNrkV+1xgkAQRSYF0IFQQewAqCB0EBwKiFZgrABTgMOVQDrACtQKtAQ7SPbgOTLHfWIy/nBnnsPI7dfd273F9x4k/tM5frFeuT0F9JsR3gj8eYY3B8KF8E2ovSK62JibWDosCTkc1IQjHHoI4JWQEEICIyxNAUwMTnmGFRyuyRgzrOfBrbAjc1pfux8CN7I9/RCqEboldHNXxWScojTwzG6ruzM9gShzjeEFiHYVfq6HQeYdNyLxzGXltMKZLjVOP0G4pIdgsK6IlrBV6uu4yzYHkXSsjHvPZ0IDBzJZtzY728qMM0TKHE6TszcdbPMta4agMl0DiVGnqi0OsSNh79/3loy8fovorNCsYZupHHODO012ITjQl7xnXCVHgYhScjWjyke11be2Oht/SRRRQ5gIwXHmphbaxnJKHHM9tAHpJQH7lY75y+k/3IJTk+OdSPs/kkwkrS+hvXdXj5bfWAPW+wJ5OAG+2pIROs1IpwHKj9n06g36bmkoj8bQKj3YCNA2na7F6o5sF7CRuE0g3dlUtqOMYlSaq/q+r2kWDI2BM3JvRbhuzR46qe6ysR32+KX/0WupqmHPAzk39w178vE2Fm6na+PZuYy3D5Pn+4T5FWAA1S+vpNfNYb8AAAAASUVORK5CYII="
                  style="padding-right:5px; border:none;"
                  alt="fb"
                >
              </a>
              <a v-if="locale.hasOwnProperty('twitterLink')" :href="locale.twitterLink" target="_blank" style="text-decoration:none;">
                <!-- src/img/icon-twitter-30.png -->
                <img
                  src="data:image/png;base64,iVBORw0KGgoAAAANSUhEUgAAAB4AAAAeCAYAAAA7MK6iAAAAGXRFWHRTb2Z0d2FyZQBBZG9iZSBJbWFnZVJlYWR5ccllPAAAAjVJREFUeNrsVutRwkAQPhwLwApIKiBUQKzAWIHJpAClAmMFYAEMWAGxAqACYwWBCkwHunf3HRzhjjxw+MXOLAFyt9++dxm70oWoU/vkNO/SZ0D8QMy/e3iTERfEn8Qpi93if4Al4Jg4JN4Qr4i/AcigQJ/YJ3aI58SjKgU6FaDcwhkseiNh84rzXLlXeCSi82nzIHAh0/yXeNzi7hh3w6YXg3YXjYoH9VwtY5qLWMXu6KzUld7iyrvlmJuAZyJRYtetmXghYsqQWAz35ziTi4SM3cgOLAX9IDHURUfzQFQ6u0RWZ1p5KboTVspwzXa/QTelw4HI4MPs9fHkMVtCEQZLPSg5EIJlqTGtzBhkFZDNbMBD0QTMNIGwnMAX9HwSliolpTXvO5fHrq5ECtlWYG7N1gLMLR9oCniwRKculIhK/28he0e3BoCVBVgJHYnOZKa+5f8VWq3VYhOlmmVexdngRKhYFbB/8GsfO+6qRUW7dDAsTDKLU8B8CPQMF1VmbgggQSnpoB4GSWbp5z3ItgKvy2kPoQ5c6GAIeNp7H/XMRGnZQ7A+BZxqblO0BIdaWalevMA77o17sjazhICVY29qmQlqdIDOo6wMtNaoh+ZDKGOavzIkX+JM7CZ1h0R6VI/SrYoKo4XHfT8wDQnbBR8jLTljMiWQ4TfbQPbNvdYqY1mVItvW0qm0XNZum9XnsdSvWy17L8TPWvuzLXsMzWZy3rJnXm+H5YaP7F43WW+vdDH6E2AAXj/qFxZQOa8AAAAASUVORK5CYII="
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
                <!-- src/img/icon-insta-30.png -->
                <img
                  src="data:image/png;base64,iVBORw0KGgoAAAANSUhEUgAAAB4AAAAeCAYAAAA7MK6iAAAAGXRFWHRTb2Z0d2FyZQBBZG9iZSBJbWFnZVJlYWR5ccllPAAAAftJREFUeNrsVu1xgkAQRccCLhWoFUQrECsIqUCdFBCpIKYCK2CCFagVgB2QCg46oINk4R56bjgPJo7+cWfWD9jPu7fvznEeciPpNLYMpKBPj3RCOmBvU9ID6c55G+bXSawSfpDO8WRHmpHG+O+S9lFUISHpp62AjiVpEXRLmiNYaLGfo8ii2Feyj02mXUuQCB2OrUkLUTZj+ESI0UKKTgP5096RFa5iuM2WWu2pBFAWDFgDS7r0DGCB/ILfkO95r8Z5iT31taQRkiaWxO+lBnKKRD7AtyZdmDs+desf9zSQSwQcG5GqltNFYWt07WtYKTp/0v05uDwNJJW8kG4s4+ECzaPSVn3rgMu1catd6gkQ6TQYsxkC7jG71QzXAXKH2KEp8QAMxGVGySaswxDzGmlkUrFayvy/sXIXwRUbnm3wewsyWaH7DMs8PRb5F/0JbBoQyLlkJQspJhKsuBh7Wb3PDDFyW2LXUkQClDtah6nFZ8RtejUE0LcEWYAOJf4LbZlN8swT844PHPY1fJyUTFTss9Ihnl0Sj4O2WwN7h3H0HqgWWvK8nE+lOSOg2VkSFUvwMa3j6hWcFVO1o8xqL6caX8sSgBXv3/uQuMWx6LW7gZzIPcShkTdMKHBQzMsJMFwg7nb1ufZlTzRdof9cb4VGha2vtw+5mfwKMACC1ecLNTFvfwAAAABJRU5ErkJggg=="
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
                <!-- src/img/icon-linkedin-30.png -->
                <img
                  src="data:image/png;base64,iVBORw0KGgoAAAANSUhEUgAAAB4AAAAeCAYAAAA7MK6iAAAACXBIWXMAAAsTAAALEwEAmpwYAAAD7ElEQVRIicWWT2wUdRTHP9/ftN22dhdSBUqaBhG2nbYxaUkDpIkk4NGAIpEbaogeNMGLB+NBNNyMhMRo5CTGcGuioNw40EQNimlYI3ZnlkZirC0GK4X+33Y7z0PZ0i4b6G4hfk/z5v3e+8x7v/ebGfifpFIWX7x4sSYejyckxauqquokzWWz2fF4PD5+6dKlsYMHD84/NLCZeeG1sM3l3P7Ioj2SWoD1gANM0oxF9qekFHB20k1e2Jbc9s+qwGEYtkh617DngbUrKCSSFEREnyQeS5xubGycKhkcBMEBOX0MNK4AWKh54FysKvbG5s2b/14R2MxcJpN5G/EeEC8DupgKkfLkHUomk+kHgsMwPIz4FKhZBXQZPFYZe66wcrfUuHr16i7ERwVQA7LA3J3rUiSMzuxs9uTQ0FBtUXAqlVo7b/MfAvVLoZK+nJme2eI5bwfwW4ngBTjsHZscO1QUHKuN7RPaXhCUw/iso6NjKJlMpjDOlgEG8DCOXB64vG4ZeHBwsMbhXqWg9UKeyfYFQfB4JpN5CthVJhih1uqoes8y8MTERBOws3CxYQ7jHee578ysF5UPBpzQCz09PR5ABYCk7RSf4gjjlJn9csc+jGgn4hhwG0cVxpuI0zZvP1BBjIgDQq8AsSJVd/g7/AQwmge3W5GBlWSRovOtza1fAwRh8IykrcBp3/eH+/r6KmvW1HzfnmxP5WN6e3svbNi44S8n9wEFWwdsqp2rrQNGHYDJ1q+8Y3fV1dU1t27NuoEwDF8KguBpgN27d+fq19afwLhdJKQ6m80mAJyZCaOyHLCZeTdHb55E9CDOX7lyZQtAQ0PDpGG/FgmRmVUCOElm2Fg54OHh4RjQDSCpobq6ummRII0WCYmACbizB86538sBj4yMCPDydo5cVf7asHuGxsxumNn4IpiIPkp/HZajzPj4+NhdMASSrj1qqpO70N3dPb0Ibm5u/hfj20fMvRVF0Zm8kT/H1t/f/7lX4R0CnliyWBh705n0JgAMH6Na0uvpTHrMmasys4S08HWV6cV0Jt2+EKitLEukb1r8lvRde4kvyARHhY5y78FfrYYwnvV9P5O/sRRgszOzxw07y8MdtDGTvbUUCkX+QNJ/pDdqRl9J2lnMX5LElGHvtza3Hi903dPStifbrltkBww7w8KBL0tmdpOII37SP1HMX3Qv29rars/OzL5s2DFgpERmhPjRnO33ff+UpKIP/6BWamBgoDUX5V5zcvvM7Kn7xExj/CRPX0xNTJ3r7Oy8dd/EK6gAM1MYhvVRFLVVVFR0RURbZEoAcya7IVO/mf1cV1c32NTUNL2SnGXLzFY1eP8BjoOLCZUAInYAAAAASUVORK5CYII="
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
