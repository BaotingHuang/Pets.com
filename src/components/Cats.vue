  <template>
  <div class="container-fluid body">
    <back-to-top bottom="50px" right="50px">
      <b-button size="lg" variant="warning" class="btn btn-info btn-to-top">
        <font-awesome-icon :icon="['fas', 'angle-up']" :style="{ color: 'white' }" />
      </b-button>
    </back-to-top>
    <b-container>
      <b-row>
        <filter-sec @filterParam="reload($event)"></filter-sec>
      </b-row>
      <b-row>
        <b-card-group deck class="mt-5 ml-2">
          <!--所有card都在同一個card deck-->
          <div v-bind:key="index" v-for="(data, index) in pets">
            <b-card
              class="card col-auto text-center mb-3 hovereffect shadow"
              style="width: 20rem;"
              img
              img-fluid
              img-top
              img-responsive
              alt="Image"
              v-if="data.album_file"
              :img-src="data.album_file"
            >
              <div class="overlay">
                <router-link class="info" :to="`/animal/${data.animal_id}`">帶我回家</router-link>
              </div>

              <!--sex-->
              <div>
                <span class="detail-text --title">
                  <font-awesome-icon :icon="['fas', 'paw']" />性別
                </span>
                <span class="detail-text" v-if="data.animal_sex == 'M'">男生</span>
                <span class="detail-text" v-else-if="data.animal_sex == 'F'">女生</span>
                <span class="detail-text" v-else>未知</span>
              </div>
              <!--size-->
              <div>
                <span class="detail-text --title">
                  <font-awesome-icon :icon="['fas', 'paw']" />體型
                </span>
                <span class="detail-text" v-if="data.animal_bodytype == 'SMALL'">小型</span>
                <span class="detail-text" v-else-if="data.animal_bodytype == 'MEDIUM'">中型</span>
                <span class="detail-text" v-else>大型</span>
              </div>
              <!--location-->
              <div>
                <span class="detail-text --title">
                  <font-awesome-icon :icon="['fas', 'paw']" />所在地
                </span>
                <span class="detail-text">{{data.animal_place.slice(0,3)}}</span>
              </div>
            </b-card>
          </div>
        </b-card-group>
      </b-row>
    </b-container>
    <infinite-loading
      :distance="distance"
      spinner="circles"
      @infinite="infiniteHandler"
      :identifier="reloadKey"
    >
      <div slot="no-results">
        <font-awesome-icon :icon="['fas', 'paw']" />沒有您所選擇的寵物待領養 : )
      </div>
      <div slot="no-more">
        <font-awesome-icon :icon="['fas', 'paw']" />已列出所有寵物
      </div>
    </infinite-loading>
  </div>
</template>
    
<script>
import axios from "axios";
import Filter from "../components/Filter.vue";

let api = "https://petscom.herokuapp.com"

export default {
  components: {
    "filter-sec": Filter
  },
  methods: {
    //infinite loading
    infiniteHandler($state) {
      let key =
        "?top=" +
        this.top +
        "&skip=" +
        this.skip +
        "&animal_kind=貓" +
        this.query; //query params
      axios.get(api + key).then(response => {
        if (response.data.length > 0) {
          this.pets = this.pets.concat(response.data);
          this.skip += 20; //keep on loading 20 more
          $state.loaded();
        } else {
          $state.complete();
        }
      });
    },
    reload(params) {
      this.reloadKey += 1;
      this.query = params;
      this.pets = [];
      this.skip = 0;
      // eslint-disable-next-line no-console
      console.log(this.query, this.reloadKey);
    }
  },
  data() {
    return {
      pets: [],
      top: 20,
      skip: 0,
      query: "",
      reloadKey: +new Date(),
      distance: 500
    };
  }
};
</script>
<style lang="scss" scoped>
@import "../css/card.scss";
</style>