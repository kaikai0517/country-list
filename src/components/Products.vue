<template>
  <div>
    <section class="container-fluid main pb-5 pt-3 px-5">
      <div class="row pb-5 justify-content-center">
        <div class="col-md-11">
          <input
            type="text"
            class="form-control mb-3"
            placeholder="搜尋產品 "
            v-model="filterText"
          />
          <select
            name=""
            id=""
            class="form-control text-dark1"
            v-model="selected"
          >
            <option value="0">預設排序</option>
            <option value="1">國名排序A~Z</option>
            <option value="2">國名排序Z~A</option>
          </select>
          <table class="table mt-4">
            <thead>
              <tr>
                <th>國旗</th>
                <th>國家名稱</th>
                <th width="120">2位國家代碼</th>
                <th width="120">3位國家代碼</th>
                <th class="text-center">母語名稱</th>
                <th>替代國家名稱</th>
                <th width="120">國際電話區號</th>
              </tr>
            </thead>
            <tbody>
              <tr v-for="item in filterTodo" :key="item.id">
                <td class="align-middle">
                  <img :src="item.flags.png" class="img-fluid" alt="" />
                </td>
                <td class="align-middle">
                  <a
                    href="#"
                    data-toggle="modal"
                    data-target="#exampleModal"
                    @click="getSingleCountry(item.name.official)"
                    >{{ item.name.official }}</a
                  >
                </td>
                <td class="align-middle text-center">{{ item.cca2 }}</td>
                <td class="align-middle text-center">{{ item.cca3 }}</td>
                <td class="align-middle">
                  <p v-for="item in item.name.nativeName" :key="item.id">
                    {{ item.official }}
                  </p>
                </td>
                <td class="align-middle">
                  <p v-for="item in item.altSpellings" :key="item.id">
                    {{ item }}
                  </p>
                </td>
                <td class="align-middle d-flex align-items-center">
                  {{ item.idd.root }}
                  <select name="" id="" class="form-control text-dark1 ml-1">
                    <option v-for="item in item.idd.suffixes" :key="item.id">{{
                      item
                    }}</option>
                  </select>
                </td>
              </tr>
            </tbody>
          </table>
          <nav aria-label="Page navigation example ">
            <ul class="pagination d-flex justify-content-center my-5 ">
              <li class="page-item" :class="{ disabled: page === 1 }">
                <a class="page-link" href="#" @click.prevent="page = page - 1"
                  >Previous</a
                >
              </li>
              <li
                class="page-item "
                v-for="index in pagination"
                :key="index"
                :class="{ active: page === index }"
              >
                <a class="page-link" href="#" @click.prevent="page = index">{{
                  index
                }}</a>
              </li>
              <li class="page-item" :class="{ disabled: page === pagination }">
                <a class="page-link" href="#" @click.prevent="page += 1"
                  >Next</a
                >
              </li>
            </ul>
          </nav>
        </div>
      </div>
    </section>
    <div
      class="modal fade"
      id="exampleModal"
      tabindex="-1"
      role="dialog"
      aria-labelledby="exampleModalLabel"
      aria-hidden="true"
    >
      <div class="modal-dialog modal-dialog-centered" role="document">
        <div class="modal-content" style="background-color:#FFEEDD">
          <div class="modal-body">
            <h2 class="text-secondary mb-4">{{ official }}</h2>
            <div class="d-flex">
              <p>首都：</p>
              <div>
                <p v-for="item in singleCountry.capital" :key="item.id">
                  {{ item }}
                </p>
              </div>
            </div>
            <p>開車方向：{{ side }}</p>
            <div class="d-flex">
              <p>洲：</p>
              <div>
                <p v-for="item in singleCountry.continents" :key="item.id">
                  {{ item }}
                </p>
              </div>
            </div>
            <div class="d-flex">
              <p>貨幣：</p>
              <div>
                <p v-for="item in singleCountry.currencies" :key="item.id">
                  {{ item.name }}
                </p>
              </div>
            </div>

            <p v-if="singleCountry.independent === true">是否獨立：是</p>
            <p v-else>是否獨立：否</p>
            <p>地圖：<a :href="maps">google maps</a></p>
            <p>人口：{{ singleCountry.population }}</p>
            <p>地區：{{ singleCountry.region }}</p>
            <p>次地區：{{ singleCountry.subregion }}</p>
            <div class="d-flex">
              <p>時區：</p>
              <div>
                <p v-for="item in singleCountry.timezones" :key="item.id">
                  {{ item }}
                </p>
              </div>
            </div>
            <p>翻譯：{{ translations }}</p>
            <p v-if="singleCountry.Unmember === true">是否加入聯合國：是</p>
            <p v-else>是否加入聯合國：否</p>
          </div>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
export default {
  data() {
    return {
      all: [],
      pagination: 0,
      filterText: "",
      page: 1,
      selected: "0",
      singleCountry: {},
      side: "",
      official: "",
      translations: "",
      maps: ""
    };
  },
  methods: {
    getCountry() {
      const api = `${process.env.APIPATH}/v3.1/all`;
      const vm = this;
      this.$http.get(api).then(response => {
        vm.all = response.data;
        console.log(vm.all);
      });
    },
    getSingleCountry(name) {
      const api = `${process.env.APIPATH}/v3.1/name/${name}`;
      const vm = this;
      this.$http.get(api).then(response => {
        vm.singleCountry = response.data[0];
        vm.side = vm.singleCountry.car.side;
        vm.official = vm.singleCountry.name.official;
        vm.translations = vm.singleCountry.translations.zho.official;
        vm.maps = vm.singleCountry.maps.googleMaps;
        console.log(vm.singleCountry);
      });
    }
  },
  computed: {
    filterTodo() {
      const vm = this;
      var newTodo = [];
      vm.all.forEach(function(item) {
        newTodo.push(item);
      });
      vm.pagination = Math.ceil(newTodo.length / 25);
      return newTodo
        .filter(function(item) {
          return item.name.official.match(vm.filterText);
        })
        .sort(function(a, b) {
          if (vm.selected === "1") {
            return a.name.official.localeCompare(b.name.official);
          }
          if (vm.selected === "2") {
            return b.name.official.localeCompare(a.name.official);
          }
        })
        .slice((vm.page - 1) * 25, (vm.page - 1) * 25 + 25);
    }
  },
  created() {
    this.getCountry();
  }
};
</script>
