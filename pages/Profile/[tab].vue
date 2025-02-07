<script setup lang="ts">
import Activity from '~~/components/landingPage/Activity.vue';
import PersonalInfo from '~~/components/profile/PersonalInfo.vue';
import Verification from '~~/components/profile/Verification.vue';
import History from '~~/components/profile/History.vue';
import ResponsibleGambling from '~~/components/profile/ResponsibleGambling.vue';
import { useStore } from 'vuex';
import { computed, onBeforeMount } from 'vue';
import { tran } from "~~/utils/translation";
import { useRouter , useRoute } from 'vue-router';
import { getGameHistory , getProfile } from '~~/action/profile';
import auth from '~~/middleware/routerMiddleware.js';
import {linkTo, linkToTab, tabToLink } from '~~/utils/link';

definePageMeta({
  middleware: [auth]
});

const route = useRoute();
const router = useRouter();
const store = useStore();

//get tab name from router
const selectedItem = ref(linkToTab(route.params.tab.toString()));

//before component mount, call action if neccessary
onBeforeMount(() => {
    switch(route.params.tab.toString()){
        case 'game-history':
            getGameHistory(store.state.pageNumber, store, router);
            break;
        case 'general-information':
            getProfile(store, router);
            break;
    }
});

const categories = computed(() => [
    {
        name: 'General Information',
        icon: 'info',
        active: selectedItem.value === 'General Information',
    },
    {
        name: 'Verification',
        icon: 'check',
        active: selectedItem.value === 'Verification',
    },
    {
        name: 'Game History',
        icon: 'game',
        active: selectedItem.value === 'Game History',
    },
    {
        name: 'Responsible Gambling',
        icon: 'hands',
        active: selectedItem.value === 'Responsible Gambling',
    },
]);

//when user click tab, change selected item and redirect
function selectCategory(val: string) {
    selectedItem.value = val;
    router.push(linkTo(`/profile/${tabToLink(val)}`));
}
</script>
<template>
    <q-page>
        <div
            style="max-width: 1450px"
            class="w-full px-0 md:px-6 lg:px-14 py-8 m-auto"
        >
            <section class="main h-full px-4">
                <div class="grid grid-cols-12 gap-6">
                    <div
                        class="col-span-12 md:col-span-6 lg:col-span-5 xl:col-span-3"
                    >
                        <p class="text-base font-bold py-3 text-center">
                            {{tran('Profile', store.state.lang)}}
                        </p>
                        <ProfileUser />
                    </div>
                    <div
                        class="col-span-12 md:col-span-6 lg:col-span-7 xl:col-span-9"
                    >
                        <CategoryBar :selectCategory="selectCategory" :categories.sync="categories" />
                        <div class="p-1"></div>
                        <ResponsibleGambling v-if="selectedItem === 'Responsible Gambling'" />
                        <History v-if="selectedItem === 'Game History'" />
                        <PersonalInfo v-if="selectedItem === 'General Information'" />
                        <Verification v-if="selectedItem === 'Verification'" />
                    </div>
                </div>
                
                <div
                    v-if="selectedItem === 'Responsible Gambling'"
                    class="w-full px-0 md:px-6 lg:px-14 py-8 m-auto font-sans font-medium"
                >
                    <profile-gambling />
                </div>
            </section>
            <section class="pt-8">
                <Activity />
            </section>
        </div>
    </q-page>
</template>
