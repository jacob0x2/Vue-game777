<script setup lang="ts">
import Activity from '~~/components/landingPage/Activity.vue';
import Deposit from '~~/components/wallet/Deposit.vue';
import DepositHistory from '~~/components/wallet/DepositHistory.vue';
import WithdrawHistory from '~~/components/wallet/WithdrawHistory.vue';
import Withdraw from '~~/components/wallet/Withdraw.vue';
import Balances from '~~/components/wallet/Balances.vue';
import { computed , onBeforeMount } from 'vue';
import { useRouter , useRoute } from 'vue-router';
import {getDepositHistory, getWithdrawHistory, getPaymentMethods} from '~~/action/wallet';
import { useStore } from 'vuex';
import { tran } from "~~/utils/translation";
import auth from '~~/middleware/routerMiddleware.js';
import {linkTo, linkToTab, tabToLink} from '~~/utils/link';

definePageMeta({
  middleware: [auth]
});

const store = useStore();
const route = useRoute();
const router = useRouter();

//get tab name from router
const selectedItem = ref(linkToTab(route.params.tab.toString()));

//before component mount, call action if neccessary
onBeforeMount(()=>{
    switch(route.params.tab.toString()){
        case 'deposit':
            getPaymentMethods(store, router);
            break;
        case 'deposit-history':
            getDepositHistory(store.state.pageNumber, store, router);
            break;
        case 'withdraw-history':
            getWithdrawHistory(store.state.pageNumber, store, router);
            break;
    }
});

const categories = computed(() => [
    {
        name: 'Balances',
        icon: 'balance',
        active: selectedItem.value === 'Balances',
    },
    {
        name: 'Deposit',
        icon: 'deposit',
        active: selectedItem.value === 'Deposit',
    },
    {
        name: 'Withdraw',
        icon: 'deposit',
        active: selectedItem.value === 'Withdraw',
    },
    {
        name: 'Deposit History',
        icon: 'history',
        active: selectedItem.value === 'Deposit History',
    },
    {
        name: 'Withdraw History',
        icon: 'history',
        active: selectedItem.value === 'Withdraw History',
    },
]);

//when user click tab, change selected item and redirect
function selectCategory(val: string) {
    selectedItem.value = val;
    router.push(linkTo(`/wallet/${tabToLink(val)}`));
}
</script>
<template>
    <q-page>
        <div
            style="max-width: 1450px"
            class="w-full px-0 md:px-6 lg:px-14 py-8 m-auto"
        >
            <section class="main h-full px-4">
                <div
                    class="wallet_baner w-full h-40 font-bold text-xl flex justify-center flex-col text-center"
                >
                    <div>
                        <img class="w-40 m-auto" src="/imgs/logo_full.png" alt="logo" />
                        <p style="color: #ffff03" class="font-black text-5xl">
                            {{tran('Europe #1', store.state.lang)}}
                        </p>
                        <p>{{tran('Online casino', store.state.lang)}}</p>
                    </div>
                </div>

                <div class="w-full py-4 flex items-center justify-center">
                    <p class="font-bold text-base hidden lg:!block">{{tran('WALLET', store.state.lang)}}</p>
                    <div
                        class="mx-2 hidden lg:!block"
                        style="width: 1px; height: 20px; background: #383d47"
                    ></div>
                    <div>
                        <CategoryBar
                            :selectCategory="selectCategory"
                            :categories.sync="categories"
                        />
                    </div>
                </div>

                <DepositHistory v-if="selectedItem === 'Deposit History'" />
                <WithdrawHistory v-if="selectedItem === 'Withdraw History'" />
                <Deposit v-if="selectedItem === 'Deposit'" />
                <Withdraw v-if="selectedItem === 'Withdraw'" />
                <Balances v-if="selectedItem === 'Balances'" />
            </section>
            <section class="pt-8">
                <Activity />
            </section>
        </div>
    </q-page>
</template>
