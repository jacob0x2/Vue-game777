<script setup lang="ts">
import { ref } from 'vue';
import CasinoSportToogleButton from '~~/components/header/CasinoSportToogleButton.vue';
import LoginRegisterButton from '~~/components/header/LoginRegisterButton.vue';
import SearchInput from '~~/components/header/SearchInput.vue';
import SideBarItem from '~~/components/sidebar/SideBarItem.vue';
import SideBarMenu from '~~/components/sidebar/SideBarMenu.vue';
import {linkTo} from '~~/utils/link';
import { useStore } from 'vuex';
import { tran } from "~~/utils/translation";
import Cookies from 'js-cookie';

const store = useStore();
interface SideBarItemInterFace {
    title: string;
    backUrl: string;
    iconUrl: string;
    link: string;
}
const sideBarLinks: SideBarItemInterFace[] = [
    {
        title: 'DAILY CASH BACK',
        backUrl: 'side_1.png',
        iconUrl: 'cash_back.png',
        link: '/bonus/cash-back',
    },
    {
        title: 'BONUS WHEEL',
        backUrl: 'side_2.png',
        iconUrl: 'wheel.png',
        link: '/wheel',
    },
    {
        title: 'BONUS PROMOTIONS',
        backUrl: 'side_3.png',
        iconUrl: 'promotion.png',
        link: '/promotions',
    },
    // {
    //     title: 'TOURNAMENTS',
    //     backUrl: 'side_4.png',
    //     iconUrl: 'tournament.png',
    //     link: '/tournament',
    // },
];
function onItemClick(item: any) {
    let lang = item?.icon;
    store.commit('handleSetLanguage', lang);
    Cookies.set("lang", lang);
}

const langs = [
    {
        name: 'English',
        icon: 'en',
    },
    {
        name: 'Spanish',
        icon: 'es',
    },
    {
        name: 'Portuguese',
        icon: 'pt',
    },
    {
        name: 'French',
        icon: 'fr',
    },
    {
        name: 'Serbian',
        icon: 'sr',
    },
    {
        name: 'Germany',
        icon: 'gm',
    },
    {
        name: 'Italy',
        icon: 'it',
    },
    {
        name: 'Poland',
        icon: 'pl',
    },
];
</script>
<template>
    <!-- SideBar -->
    <q-drawer
        v-model="store.state.isDrawer"
        show-if-above
        :width="270"
        :breakpoint="1064"
        class="p-3 overflow-x-hidden"
        style="background-color: #181a25"
        @before-hide="store.commit('handleDrawer', false)"
    >
        <div
            style="border-bottom: 1px solid #7d8396"
            class="w-full text-center py-3 lg:hidden"
            v-if="store.state.isLogin === true"
        >
            <q-btn-dropdown
                class="btn-none !rounded-3xl"
                label-class="d-flex align-items-center"
            >
                <template v-slot:label>
                    <p class="text-xl font-bold">{{ store.state.balance.total_balance }} {{ store.state.balance.currency }}</p>
                </template>
                <q-item
                    clickable
                    v-close-popup
                    @click="$router.push(linkTo('/wallet/balances'))"
                >
                    <q-item-section>
                        <q-item-label>
                            <div class="flex justify-between">
                                <p class="text-xs sm:text-xs pl-1">{{tran('Cash Balance:', store.state.lang)}} </p>
                                <p class="text-xs sm:text-xs pl-1">{{ store.state.balance.balance }} {{ store.state.balance.currency }}</p>
                            </div>
                        </q-item-label>
                    </q-item-section>
                </q-item>
                <q-item
                    clickable
                    v-close-popup
                    @click="$router.push(linkTo('/wallet/balances'))"
                    class="border-b-2"
                >
                    <q-item-section>
                        <q-item-label>
                            <div class="flex justify-between">
                                <p class="text-xs sm:text-xs pl-1">{{tran('Bonus Balance:', store.state.lang)}} </p>
                                <p class="text-xs sm:text-xs pl-1">{{ store.state.balance.bonus_balance }} {{ store.state.balance.currency }}</p>
                            </div>
                        </q-item-label>
                    </q-item-section>
                </q-item>
                <q-item
                    clickable
                    v-close-popup
                    @click="$router.push(linkTo('/wallet/balances'))"
                >
                    <q-item-section>
                        <q-item-label>
                            <div class="flex justify-between ">
                                <p class="text-xs sm:text-xs pl-1">{{tran('Total Balance:', store.state.lang)}} </p>
                                <p class="text-xs sm:text-xs pl-1">{{ store.state.balance.total_balance }} {{ store.state.balance.currency }}</p>
                            </div>
                        </q-item-label>
                    </q-item-section>
                </q-item>
            </q-btn-dropdown>
            <p style="color: #7b8193" class="text-s">{{store.state.User.username}}</p>
            <!-- <p class="text-xs">
                <span style="color: #fff004">LEVEL : </span><span>VIP</span>
            </p> -->
        </div>
        <!-- <div
            class="py-3 w-full text-center md:hidden"
            style="border-bottom: 1px solid #7d8396"
            v-if="store.state.isLogin === true"
        >
            <q-btn @click="$router.push(linkTo('/wallet/deposit'))" color="primary">
                <div class="flex items-center justify-start py-1">
                    <img class="w-7" src="/imgs/deposit_white.png" alt="test" />
                    <p class="font-semibold text-xl pl-2">DEPOSIT</p>
                </div>
            </q-btn>
        </div> -->
        <div>
            <div class="mt-5 text-center">
                <LoginRegisterButton
                    v-if="store.state.isLogin === false"
                />
            </div>
        </div>
        <q-list class="">
            <SideBarItem
                v-for="item in sideBarLinks"
                :key="item.title"
                v-bind="item"
            />
            <SideBarMenu />
        </q-list>
        <q-btn-dropdown
            class="lg:hidden btn-none float-right w-full"
            label-class="d-flex align-items-center"
        >
            <template v-slot:label>
                <div
                    class="flex flex-nowrap justify-start text-sm w-full"
                >
                    <q-img
                        class="w-7 mr-3"
                        :src="`/imgs/header/${store.state.lang}_large.png`"
                        alt="lang"
                    />
                    {{ langs.filter(item => item?.icon == store.state.lang)[0].name }}
                </div>
            </template>

            <q-list>
                <q-item
                    v-for="lang in langs"
                    clickable
                    v-close-popup
                    @click="onItemClick(lang)"
                >
                    <q-item-section>
                        <q-item-label>
                            <div class="flex items-center justify-start">
                                <q-img
                                    class="w-3"
                                    :src="`/imgs/header/${lang.icon}.png`"
                                    alt="lang"
                                />
                                <p class="text-md pl-1">{{ lang.name }}</p>
                            </div>
                        </q-item-label>
                    </q-item-section>
                </q-item>
            </q-list>
        </q-btn-dropdown>
    </q-drawer>
</template>
