<template>
  <div class="confirm-factory-page">
    <v-container style="max-width: 630px; position: relative;" class="pt-3 pt-md-12">
      <h2 class="mt-2 mb-2 secondary--text">填寫聯絡資訊</h2>
      <p>為了驗證通報資料，我們可能會與您聯繫</p>

      <h3 class="mt-5 mb-2 primary--text">姓名</h3>
      <v-text-field outlined v-model="formState.contactName" placeholder="例：王小明" color="primary" />

      <h3 class="mt-5 mb-2 primary--text">手機或電話</h3>
      <v-text-field outlined v-model="formState.contactPhone" placeholder="例：09XX-XXXXXX 或 0X-XXXXXXX" color="primary" />

      <h3 class="mt-5 mb-2 primary--text">Email</h3>
      <v-text-field outlined v-model="formState.contactMail" placeholder="例：bear@bear.com" color="primary" />

      <h2 class="mt-2 mb-2 secondary--text">授權條款</h2>

      <div>本授權條款允許使用者重製、散布、傳輸以及修改著作（包括商業性利用）。若使用者修改該著作時，僅得依本授權條款或與本授權條款類似者來散布該衍生作品。使用時必須按照著作人指定的方式表彰其姓名。本授權條款允許使用者重製、散布、傳輸以及修改著作（包括商業性利用）。若使用者修改該著作時，僅得依本授權條款或與本授權條款類似者來散布該衍生作品。使用時必須按照著作人指定的方式表彰其姓名。</div>

      <v-checkbox v-model="formState.agree" :label="'我同意授權條款'"/>

      <!-- <h2 class="mt-2 mb-2 secondary--text">確認及補充工廠資訊</h2>

      <p>請確認黑熊出沒痕跡地點及照片，並補充痕跡資訊。</p>

      <h3 class="mt-7 mb-2 required primary--text">黑熊出沒痕跡地點</h3>

      <p>
        返回<a @click="gotoStepOne">步驟(1/3)</a>編輯
      </p> -->

      <!-- <div class="d-flex flex-column flex-md-row justify-md-between">
        <div class="w-100 minimap-container" style="position: relative;" :class="{ desktop: $vuetify.breakpoint.mdAndUp }">
          <minimap
            :initialFactories="initialFactories"
            :initialLocation="initialLocation"
            :pinLocation="appState.factoryLocation"
          />
          <div class="minimap-overlay" @click="gotoStepOne" />
        </div>

        <div class="d-flex justify-between mt-3 mt-md-0 ml-md-4 d-md-inline-flex flex-md-column justify-md-start">
          <p>
            經度：{{ appState.factoryLocation[0].toFixed(7) }}
            <br>
            緯度：{{ appState.factoryLocation[1].toFixed(7) }}
          </p>

          <p>
            <small>以上經緯度版本為WGS84</small>
          </p>
        </div>
      </div>

      <h3 class="mt-7 mb-2 required primary--text">黑熊出沒痕跡照片</h3>

      <p>
        返回<a @click="gotoStepTwo">步驟(2/3)</a>編輯
      </p>

      <div class="preview-images-container mb-2">
        <div v-for="image of previewImages" :key="image.token" class="uploaded-image">
          <img :src="image.src" />
        </div>
      </div>

      <h3 class="mt-5 mb-2 primary--text">聯絡人暱稱</h3>

      <p>{{ formState.nickname || '未填寫'  }}</p>

      <h3 class="mt-5 mb-2 primary--text">聯絡方式 (email或電話)</h3>

      <p>{{ formState.contact || '未填寫' }}</p>

      <hr>

      <h2 class="mt-5 secondary--text mb-5">其他黑熊出沒痕跡資訊（非必填）</h2>

      <p>提供明確的黑熊出沒痕跡資訊能夠幫助我們更快速地搜索追蹤。</p>

      <h3 class="mt-5 mb-2 primary--text">黑熊出沒痕跡描述</h3>

      <v-textarea outlined solo v-model="formState.others" placeholder="例：雞舍疑似有熊造訪" />

      <h3 class="mt-5 mb-2 primary--text">疑似黑熊出沒地點可鑑別特徵，例如雞舍招牌名稱</h3>

      <v-text-field outlined v-model="formState.name" placeholder="例：雞舍名稱" color="primary" />

      <h3 class="mt-5 mb-2 primary--text">黑熊出沒痕跡類型</h3>

      <v-select :items="reportTypeItems" v-model="formState.type" solo outlined placeholder="未選擇" /> -->

      <div class="bottom-button-container w-100 d-flex justify-center align-items-center px-xs-3 pb-md-9">
        <v-btn x-large rounded @click="submit" style="width: 100%; max-width: 345px;" v-bind="attrs" v-on="on" color="primary">
          確認送出
        </v-btn>
      </div>
    </v-container>
  </div>
</template>

<script lang="ts">
import { createComponent, inject, ref } from '@vue/composition-api'

import { MainMapControllerSymbol } from '../symbols'
import { MapFactoryController } from '../lib/map'
import { useAppState } from '../lib/appState'
import { REPORT_TYPE } from '../types'

import Minimap from './Minimap.vue'

export default createComponent({
  name: 'ConfirmFactory',
  components: {
    Minimap
  },
  props: {
    formState: {
      type: Object,
      default: {}
    },
    previewImages: {
      type: Array,
      default: []
    },
    submit: {
      type: Function
    }
  },
  setup () {
    const mapController = inject(MainMapControllerSymbol, ref<MapFactoryController>())
    const [appState, { pageTransition }] = useAppState()

    const initialFactories = mapController.value?.factories
    const initialLocation = mapController.value?.mapInstance.map.getView().getCenter()

    const reportTypeItems: Array<{ text: string, value?: string }> = [
      ...REPORT_TYPE
    ]

    return {
      appState,
      initialFactories,
      initialLocation,
      gotoStepOne () {
        if (mapController.value) {
          pageTransition.gotoCreateStep(0)
        }
      },
      gotoStepTwo () {
        pageTransition.gotoCreateStep(1)
      },
      reportTypeItems
    }
  }
})
</script>

<style lang="scss" scoped>
@import '@/styles/components/preview-images.scss';

.confirm-factory-page {
  @import '@/styles/typography.scss';

  background-color: white;
  z-index: 1;
  position: absolute;
  width: 100%;
  height: 100%;

  padding-bottom: 50px;
  overflow-y: auto;
  overflow-x: hidden;

  padding-bottom: 72px;

  h2 {
    font-size: 24px;
  }
}

.bottom-button-container {
  background: white;
  position: fixed;
  bottom: 0;
  left: 0;
  padding: 10px 15px;
}

hr {
  color: #EAF3BF;
  border-color: #EAF3BF;
  background-color: #EAF3BF;
  height: 1px;
  border-width: inherit;
}

.minimap-container {
  &.desktop {
    max-width: 430px;
  }
}

.minimap-overlay {
  width: 100%;
  height: 100%;
  position: absolute;
  top: 0;
  left: 0;
}
</style>
