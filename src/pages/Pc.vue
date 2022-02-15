<template><q-page>
  <q-card>
    <q-card-section>
      <h3>Положение пользователей</h3>
    </q-card-section>
    <q-card-section>

  <yandex-map :coords="coord" :settings="settings" :zoom="10" @click="onClick">
      <ymap-marker
        v-for="(item, i) in coords"
        marker-id="i"
        :key="i"
        :coords="item.coord"
        :hint-content="item.content"
      ></ymap-marker>
    </yandex-map>
    </q-card-section>
  </q-card>
</q-page>
</template>

<script>
import { yandexMap, ymapMarker } from 'vue-yandex-maps'
import { computed, defineComponent, ref } from "vue";
import { useStore } from 'vuex'
import API from "../api/api.js";

export default {
  name: 'App',
  components: { yandexMap, ymapMarker },
  setup() {
        const $store = useStore()
      const userName = computed({
      get: () => $store.state.mes.userName,
      set: val => {
        $store.commit('mes/updateUserNameState', val)
      }
      })
    const messages = computed({
      get: () => $store.state.mes.messages,
      set: val => {
        $store.commit('mes/updateMessagesState', val)
      }
    })
    const intervalCtx = computed({
      get: () => $store.state.mes.intervalCtx,
      set: val => {
        $store.commit('mes/updateIntervalCtxState', val)
      }
    })
    const lastMsgID = computed({
      get: () => $store.state.mes.lastMsgID,
      set: val => {
        $store.commit('mes/updateLastMsgIDState', val)
      }
    })
    const messageText = computed({
      get: () => $store.state.mes.messageText,
      set: val => {
        $store.commit('mes/updateMessageTextState', val)
      }
    })
    // const coords = computed({
    //   get: () => $store.state.mes.coords,
    //   set: val => {
    //     $store.commit('mes/updateCoords', val)
    //   }
    // })
    let coords=ref([
            { coord: [55.726822, 37.557682], content: "Слон" },
            { coord: [55.760178, 37.618575], content: "Попугай" },
            { coord: [55.819721, 37.611704], content: "Удав" },
            // { coord: [55.82, 36.61], content: "Ёжик" },
        ])
    let location=[]
    let descr=""
    return {
      coord: [55.75, 37.61],
      coords,
      ycoords: ref([55.75, 37.61]),
      location,
      settings : {
        apiKey: '73784db1-80df-448a-95a3-6f81554055ea',
        lang: 'ru_RU',
        coordorder: 'latlong',
        enterprise: false,
        version: '2.1'
    },$store, userName, messages, intervalCtx, lastMsgID,  messageText, descr,
    }
  },
    mounted() {
    // this.userName = "Login";
    this.intervalCtx = setInterval(async () => {
      try {
        navigator.geolocation.getCurrentPosition(pos => {
          this.gettingLocation = false;
          this.location = pos;
        },)
        // setTimeout(() => {
        //     console.log("Weit4000!");
        //   }, 4000);

        // this.ycoords=[this.location["coords"]["latitude"],this.location["coords"]["longitude"]]
        // this.coords[3]['coord']=[this.location["coords"]["latitude"],this.location["coords"]["longitude"]]
        // console.log( this.coords[3]['coord'])



        let item ={}
        item ={
          coord:  [ this.location["coords"]["latitude"], this.location["coords"]["longitude"] ],
          // coord:  [ 55.77226178323436, 37.75848709106444 ],
          content: "Ёжик"
        }
        // this.coords.push(item)
        this.coords[3]=item

        console.log(this.coords)
        this.$forceUpdate()


      } catch (e) {}
    }, 1000);
  },
  unmounted() {
    clearInterval(this.intervalCtx);
  },
  computed: {
    balloonTemplate() {
      return `
        <h5 class="red">Hi, everyone!</h5>
        <p>I am here: ${this.coords.content}</p>
        <img src="http://via.placeholder.com/350x150">
      `
    }
  },
  methods: {
    // Реакция на кнопку отправки
    onSendClick() {
    let item ={}
    item ={
      coord:  [ 55.77226178323436, 37.75848709106444 ],
      content: "<p>"+ this.descr+"</p> <p></p><img src=\"4.jpg\" ></p>"
    }
    this.coords.push(item)
    this.$forceUpdate()
    console.log(this.coords)
    },
    onClick(e) {
      this.ycoords = e.get('coords');
    },
},
}
</script>
<style>
.ymap-container {
  height: 600px;
}
</style>
