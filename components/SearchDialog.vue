<script setup lang="ts">
import {  onBeforeUpdate , watch} from "vue"; 
import { useStore } from "vuex";
import { useRouter , useRoute} from "vue-router";
import { tran } from "~~/utils/translation";
import { linkTo } from "~~/utils/link";
import { addFavoriteGameById, removeFavoriteGameById , searchGames} from "~~/action/game";

const router = useRouter();
const route = useRoute();
const store = useStore();
const searchText = ref("");
const provider = ref("");
const providerList= ref([]);
const focusgame = ref("");
const providers = ref([]);
const providerHeight=ref();
let showProvider = ref(false);

onBeforeUpdate(()=>{
  focusgame.value="";
  providerList.value = store.state.providers.map(provider=>provider.name).sort();
  providers.value = providerList.value;
  provider.value = store.state.selectedProvider;
  showProvider.value=false;
  store.commit('handleSearchResult',[]);
});

watch(()=>searchText.value,()=>{  
  if(provider.value!="" && provider.value!=null || searchText.value!="")
    searchGames(searchText.value, provider.value, store);
  else
    store.commit('handleSearchResult',[]);
});

watch(()=>provider.value,()=>{
  if(provider.value!="" && provider.value!=null || searchText.value!="")
    searchGames(searchText.value, provider.value, store);
  else
    store.commit('handleSearchResult',[]);
});



const selectProvider = (item) => {
  showProvider.value=false;
  providers.value = providerList.value;
  provider.value = item;
};
const selectUnFocus = () => {
  showProvider.value=false;
}

const play = (demo, slug) => {
  store.commit("handleOnSearchDialog", false);
  store.commit("handleGamePlayMode", demo);
  router.push(linkTo(`/play/${slug}`));
};
const filterProvider = (val, update, abort) => {
  update(() => {
    const needle = val.toLowerCase();
    providers.value = providerList.value.filter(
      (v:string) => v.toLowerCase().indexOf(needle) > -1
    );
  });
};
const onFavorite = (id, slug) => {
  if (store.state.favoriteGameSlugList.includes(slug))
    removeFavoriteGameById(store, id, slug, route.query?.tab);
  else addFavoriteGameById(store, id, slug);
};

const handleFocusGame = (id) => {
  focusgame.value = id;
};
const selectFocus = () => {
  showProvider.value=!showProvider.value;
}
const imgurl = "/imgs/noGameImg.png";
</script>
<template>
  <q-dialog
    v-model="store.state.isSearchDiaolg"
    @hide="store.commit('handleOnSearchDialog', {a:false, b:''});"
  >
    <q-card style="width: 600px; max-width: 90vw" class="relative bg-gray-900">
      <q-card-section>
        <div class="grid grid-cols-1 sm:grid-cols-2 gap-5">
          <q-input
            filled
            :placeholder="tran('At least 3 characters...', store.state.lang)"
            v-model="searchText"
            dense
          >
            <template v-slot:append>
              <q-icon name="search" />
            </template>
          </q-input>
          <div>
            <q-select
              filled
              v-model="provider"
              use-input
              :placeholder="tran('Select provider', store.state.lang)"
              hide-selected
              fill-input
              @filter="filterProvider"
              @click="selectFocus"
              @popup-show="selectUnFocus"
              @clear="selectFocus"
              dense
              clearable
            >
              <template v-slot:prepend>
                <img
                  class="w-5 mr-1"
                  src="/imgs/sidebar/provider.png"
                  alt="hot"
                />
              </template>
            </q-select>
          </div>
        </div>
      </q-card-section>
      <q-card-section class="scroll duration-300 p-0" :class="showProvider?'!max-h-[250px] pb-3':'max-h-[0px]'">
        <div class="grid gap-2 grid-cols-2 sm:grid-cols-3">
          <p class="p-1 ml-5  hover:bg-gray-700 cursor-pointer" v-for="item in providers" @click="selectProvider(item)">{{ item }}</p>
        </div>
      </q-card-section>
      <q-separator />
      <q-card-section style="height: 50vh" class="scroll">
        <p v-if="store.state.searchGameList.length==0" class="py-3 text-xl font-bold text-white text-center">No search result</p>
        <div class="">
          <div class="grid grid-cols-2 sm:grid-cols-3 gap-x-1">
            <div
              class="group hidden md:!block h-full p-1 w-full"
              v-for="gameItem in store.state.searchGameList"
            >
              <div class="relative w-full rounded-lg">
                <img
                  :src="gameItem?.image ? gameItem?.image : imgurl"
                  class="relative h-full w-full rounded-lg z-[1] bg-cover"
                />
                <div class="opacity-0 group-hover:opacity-100 absolute w-full h-full top-0 left-0 z-[2] rounded-lg bg-gray-900 bg-opacity-80 transition ease-in-out duration-300">
                  <div
                    class="absolute w-full h-full flex flex-col justify-center items-center"
                  >
                    <q-btn
                      text-color="white"
                      style="
                        border-radius: 50%;
                        background-color: red;
                        padding: 5px;
                        margin-bottom: 5px;
                      "
                      @click="play(0, gameItem.slug)"
                    >
                      <q-icon name="play_arrow" size="lg" />
                    </q-btn>
                    <q-btn
                      v-if="gameItem?.demo == 1"
                      text-color="white"
                      padding="1px 10px"
                      label="Demo"
                      style="
                        font-size: x-small;
                        border-radius: 10%;
                        background-color: transparent;
                        border: white 2px solid;
                      "
                      @click="play(1, gameItem.slug)"
                    />
                  </div>
                  <q-btn
                    text-color="yellow"
                    padding="0px"
                    class="absolute top-2 right-2"
                    style="background-color: transparent"
                    @click="onFavorite(gameItem.id, gameItem.slug)"
                  >
                    <q-icon
                      v-if="
                        store.state.favoriteGameSlugList.includes(
                          gameItem?.slug
                        )
                      "
                      name="favorite"
                      size="xs"
                    />
                    <q-icon
                      v-if="
                        !store.state.favoriteGameSlugList.includes(
                          gameItem?.slug
                        )
                      "
                      name="favorite_border"
                      size="xs"
                    />
                  </q-btn>
                </div>
              </div>
              <p class="text-center text-white text-[11px] group-hover:text-[12px] p-2">
                {{ gameItem?.name }}
              </p>
            </div>
            <div
              class="md:hidden h-full w-full p-1"
              v-for="gameItem in store.state.searchGameList"
              @click="handleFocusGame(gameItem.id)"
            >
              <div class="relative w-full rounded-lg">
                <img
                  :src="gameItem.image ? gameItem.image : imgurl"
                  class="relative h-full w-full rounded-lg z-[1] bg-cover"
                />
                <div
                  class="absolute w-full h-full top-0 left-0 z-[2] rounded-lg bg-gray-900 bg-opacity-80 opacity-0 duration-300"
                  :class="focusgame == gameItem.id && 'opacity-100'"
                >
                  <div
                    class="absolute w-full h-full flex flex-col justify-center items-center"
                  >
                    <q-btn
                      text-color="white"
                      style="
                        border-radius: 50%;
                        background-color: red;
                        padding: 2px;
                        margin-bottom: 7px;
                      "
                      @click="play(0, gameItem.slug)"
                    >
                      <q-icon name="play_arrow" size="lg" />
                    </q-btn>
                    <q-btn
                      v-if="gameItem?.demo == 1"
                      text-color="white"
                      padding="1px 5px"
                      label="Demo"
                      style="
                        font-size: x-small;
                        border-radius: 10%;
                        background-color: transparent;
                        border: white 2px solid;
                      "
                      @click="play(1, gameItem.slug)"
                    />
                  </div>
                  <q-btn
                    text-color="yellow"
                    padding="0px"
                    class="absolute top-2 right-2"
                    style="background-color: transparent"
                    @click="onFavorite(gameItem.id, gameItem.slug)"
                  >
                    <q-icon
                      v-if="
                        store.state.favoriteGameSlugList.includes(
                          gameItem?.slug
                        )
                      "
                      name="favorite"
                      size="xs"
                    />
                    <q-icon
                      v-if="
                        !store.state.favoriteGameSlugList.includes(
                          gameItem?.slug
                        )
                      "
                      name="favorite_border"
                      size="xs"
                    />
                  </q-btn>
                </div>
                <div
                  class="absolute z-[3] w-full h-full top-0 left-0 rounded-lg"
                  v-if="focusgame != gameItem.id"
                ></div>
              </div>
              <p class="text-center gametext p-2">
                {{ gameItem?.name }}
              </p>
            </div>
          </div>
        </div>
      </q-card-section>
    </q-card>
  </q-dialog>
</template>
