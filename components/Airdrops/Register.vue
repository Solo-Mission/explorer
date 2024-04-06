<template>
  <AuthFrame
    :title="$t('register_title')"
    :subtitle="$t('register_subtitle')"
    type="register"
  >
    <div>
      <div class="head">
        <h3 class="use-text-title2">
          {{ $t('register') }}
        </h3>
        <v-btn size="small" variant="text" class="button-link" :href="'/menus/login'">
          <v-icon>mdi-arrow-right</v-icon>
          {{ $t('register_already') }}
        </v-btn>
      </div>

      <v-form
        ref="form"
        v-model="valid"
      >
        <v-row class="spacing3">
          <v-col cols="12" sm="12" class="px-3">
            <v-text-field
              v-model="username"
              :label="$t('register_name')"
              :rules="requiredRules"
              color="secondary"
              variant="filled"
              name="username"
              required
            />
          </v-col>
        </v-row>
        <div class="btn-area flex">
          <div class="form-helper pb-5">
            <div class="form-control-label">
              <v-checkbox
                v-model="checkbox"
                :label="$t('form_terms')"
                :rules="requiredRules"
                color="secondary"
                required
                hide-details
                class="ms-n2"
              />
              <span>
                <a href="#" class="link">
                  {{ $t('form_privacy') }}
                </a>
              </span>
            </div>
          </div>
          <v-btn
            :block="isTablet || isMobile"
            size="large"
            color="primary"
            @click="handleSubmit"
          >
            {{ $t('sign up') }}
          </v-btn>
        </div>
            <!-- 分隔符 -->
        <div class="separator">
          <span class="line"></span>
          <span class="or">OR</span>
          <span class="line"></span>
        </div>
        <div class="btn-area">
          <v-btn
            size="large"
            block
            color="primary"
            @click="handleSubmit_directly"
          >
            {{ $t('Sign in with passkey directly') }}
          </v-btn>
        </div>
      </v-form>
    </div>
  </AuthFrame>
</template>

<style lang="scss" scoped>
@import './form-style';
</style>

<script setup>
import { ref } from 'vue';
import { useDisplay } from 'vuetify';
import { useRouter } from 'vue-router';
import link from '@/assets/text/link';
import SocialAuth from './SocialAuth';
import AuthFrame from './AuthFrame';

const form = ref(null);
const valid = ref(true);
const email = ref('');
const username = ref('');
const emailRules = ref([]);
const checkbox = ref(false);
const router = useRouter();
const { md: isTablet } = useDisplay();
const { xs: isMobile } = useDisplay();
const { smAndDown: isMobile2 } = useDisplay();

const SERVER = "http://localhost:58089";
const registrationStartUrl = SERVER + "/api/diyRegister/start";
const registrationFinishUrl = SERVER + "/api/diyRegister/finish";
const assertionStartUrlDirect = SERVER + "/api/diyLogin/start_direct";
const assertionFinishUrlDirect = SERVER + "/api/diyLogin/finish_direct";
const fetchOptions = {
            method: "POST",
            credentials: "include",
            headers: {"Content-Type": "application/json"}
        }

function handleSubmit() {
  if (valid.value) {
    console.log(username.value);
  }
  _onFormSubmit(username)

}


function handleSubmit_directly() {
  _onFormSubmit_direct();
}


async function _getPublicKeyCredentialRequestOptionsDecoder() {
    
    const {decodePublicKeyCredentialRequestOptions: e} = await import("./parse.js");
    return e;
}

async function _getLoginCredentialEncoder() {
  
    const {encodeLoginCredential: e} = await import("./parse.js");
    return e;
}

async function _getPublicKeyCredentialCreateOptionsDecoder() {
    const {decodePublicKeyCredentialCreateOptions: e} = await import("./parse.js");
    return e;
}

async function _getRegisterCredentialEncoder() {
    const {encodeRegisterCredential: e} = await import("./parse.js");
    return e;
}


async function _onFormSubmit(username) {
    try {
        if (!window.PublicKeyCredential) {
            alert("Fido is not supported on this browser");
            throw new Error("Web Authentication is not supported on this platform");
        }
        if (!PublicKeyCredential.isConditionalMediationAvailable ||
            !PublicKeyCredential.isConditionalMediationAvailable()) {
            console.info("error");
            return;
        }


        const i = await fetch(registrationStartUrl, {
            ...fetchOptions,
            body: JSON.stringify({username: username.value})
        }), {status: r, registrationId: n, publicKeyCredentialCreationOptions: s, message: m1} = await i.json();
        if (!i.ok) {
            alert(m1);
            throw new Error("Could not successfuly start registration");
        }
        // add by mark   need to delete---------------------------
        s.user.id = "DEMO//9fX19ERU1P";
        // add by mark   need to delete---------------------------
        const o = await _getPublicKeyCredentialCreateOptionsDecoder();
        const a = await navigator.credentials.create({publicKey: o(s)});

        const u = await _getRegisterCredentialEncoder();
        const l = await fetch(registrationFinishUrl, {
                ...fetchOptions,
                body: JSON.stringify({registrationId: n, credential: u(a), userAgent: window.navigator.userAgent})
            }), c = await l.json();

        if (!l.ok){
            throw new Error("Could not successfuly complete login");
        }
       
        ElMessage({
          showClose: true,
          message: 'Congrats, Register success.',
          type: 'success',
        })
        router.push('/menus/login');
        
    } catch (t) {
        console.error('Error during form submission:');
    }
}


async function _onFormSubmit_direct() {
    try {
        if (!window.PublicKeyCredential) {
            alert("Fido is not supported on this browser");
            throw new Error("Web Authentication is not supported on this platform");
        }
        if (!PublicKeyCredential.isConditionalMediationAvailable ||
            !PublicKeyCredential.isConditionalMediationAvailable()) {
            console.info("error");
            return;
        }


        const n = await fetch(assertionStartUrlDirect, {
            ...fetchOptions,
            body: JSON.stringify({username: ''})
        }), {assertionId: i, publicKeyCredentialRequestOptions: s, message: m1} = await n.json();

        if (!n.ok) {
            alert(m1);
            throw new Error("Could not successfuly start login");
        }

        const r = await _getPublicKeyCredentialRequestOptionsDecoder(),
            // 这里的r是/utils/parse.js里的i函数 用于处理公钥凭证的编解码和转换操作
            o = await navigator.credentials.get({
                mediation: undefined,
                signal: undefined,
                publicKey: r(s)
            });

        const a = await _getLoginCredentialEncoder(),
            cre = a(o);
            cre.response.userHandle = s.userHandle;
        const u = await fetch(assertionFinishUrlDirect, {
            ...fetchOptions,
            body: JSON.stringify({assertionId: i, credential: cre})
        });
        if (!u.ok){
            throw new Error("Could not successfuly complete login");
        }
        const l = await u.json();
        console.info(l);
        localStorage.setItem('token', l.username);
        ElMessage({
          showClose: true,
          message: 'Congrats, login success.',
          type: 'success',
        })
        router.push('/menus/spaces');
        
    } catch (t) {
        console.info('Error during form submission:');
    }
}
</script>