<script setup lang="ts">
    import { onMounted , ref , watch} from 'vue';
    import {SignUp} from '~~/action/auth';
    import {useStore} from 'vuex';
    import { Cookies } from 'quasar';
    import countryCodes from './countryCode.json';
    import { tran } from "~~/utils/translation";

    const store = useStore();
    const tab = ref("Next");
    const model=ref('');
    
    watch(()=>model.value, ()=>{
        signupInfo.country.value = model.value;
        if(countryList.includes(model.value))
            signupInfo.countryCode.value = countryCodes.filter(cc=>cc.name==model.value)[0].code;
    });
    const tabChange = () => {
        if(tab.value == "Next") tab.value = "Prev";
        else tab.value = "Next";
    }
    const validation = () => {
        let noti:string[] = [];
        let result = true;
        Object.keys(signupInfo).map(item => {
            if(signupInfo[item].value==''){
                noti.unshift(`${item} field is required.`);
                result=false;
            }
        });
        if(!result)
            noti.map(item=>store.commit('handleNotification',{type:'Error',message: item}));
        return result;
    }
    const signUp = () => {  
        if(validation()){                                       //call register action with inputed data and fingerprint, click_id and promo
            Object.keys(signupInfo).map(item => {
                userdata = {...userdata, [item] : signupInfo[item].value};
            });
            // userdata = {...userdata, 'fingerprint': fpData.visitorId, 'click_id': Cookies.get('click_id'), 'promo': Cookies.get('promo')};
            userdata = {...userdata, 'visitorId':store.state.visitorId, 'click_id': Cookies.get('click_id'), 'promo': Cookies.get('promo')};
            SignUp(userdata, store);
        }
    }

    let userdata = {};
    let signupInfo = {
        first_name: ref(''),
        last_name: ref(''),
        username: ref(''),
        gender: ref(''),
        dob: ref(''),
        email: ref(''),
        password: ref(''),
        password_confirmation: ref(''),
        country: ref(''),
        street_1_address: ref(''),
        state: ref(''),
        city: ref(''),
        zip: ref(''),
        currency: ref(''),
        phone: ref(''),
        countryCode: ref(''),
    }; 
    signupInfo.gender.value = 'Male';

    const genders = ['Male', 'Female'];
    const currencys = ['CAD', 'USD' , 'NZD' , 'EUR' , 'AUD' , 'GBP' , 'RUB'];
    const countryList = countryCodes.map(item=>item.name);
    const countries = ref(countryList);
    const onGenderItemClick = (item: any) => {
        signupInfo.gender.value = item
    }
    const onCurrencyItemClick = (item: any) => {
        signupInfo.currency.value = item
    }
    const filterFn = (val, update, abort) => {
        // if (val.length < 3) {
        //   abort();
        //   return;
        // }
        update(() => {
          const needle = val.toLowerCase();
          countries.value = countryList.filter(v => v.toLowerCase().indexOf(needle) > -1);
        })
    };
</script>
<template>
    <q-dialog v-model="store.state.onRegister" @hide="()=>store.commit('handleOnRegister', false)">
        <q-card  class="w-full sm:w-4/5 md:w-3/5" style="width: 700px">
            <div style="background: rgb(0 90 201)">
                <div class="sm:grid sm:grid-cols-2 p-2">
                    <div 
                        class="p-1 mt-10 hidden sm:!block flex flex-nowrap justify-content-center text-center"
                    >
                        <q-img
                            class="-mb-12 mt-0"
                            style="max-width: 221px"
                            src="/imgs/new.png"
                            alt="man"
                        />
                        <q-img
                            class="-mb-2"
                            style="max-width: 221px"
                            src="/imgs/casino_offers.png"
                            alt="man"
                        />
                        <div
                            class="flex flex-nowrap justify-between items-center w-full"
                        >
                            <span style="font-size: 10px;text-align: center;"
                                >{{tran('confirm', store.state.lang)}}</span>
                        </div>
                    </div>
                    <div class="p-1 mt-7 text-center">
                        <div>
                            <p class="font-bold text-2xl text-shadow-lg text-center py-2">
                                {{tran('Sign Up', store.state.lang)}}
                            </p>
                            <p class="cursor-pointer text-xs pb-4 underline" @click="store.commit('handleOnLogin', true);store.commit('handleOnRegister', false);">
                                {{tran('alreadyAccount', store.state.lang)}}
                            </p>
                            <q-tab-panels v-model="tab" animated class="bg-transparent text-white mb-3">
                                <q-tab-panel name="Next">
                                    <div class="flex flex-nowrap items-center justify-start">
                                        <q-icon
                                            class="opacity-50 pr-1"
                                            size="sm"
                                            name="person"
                                        />
                                        <q-input
                                            class="text-shadow-lg w-full"
                                            type="text"
                                            :placeholder="tran('First Name', store.state.lang)"
                                            standout
                                            v-model="signupInfo.first_name.value"
                                            :dense="true"
                                        />
                                    </div>
                                    <div class="flex flex-nowrap items-center justify-start pt-3">
                                        <q-icon
                                            class="opacity-50 pr-1"
                                            size="sm"
                                            name="person"
                                        />
                                        <q-input
                                            class="text-shadow-lg w-full "
                                            type="text"
                                            :placeholder="tran('Last Name', store.state.lang)"
                                            standout
                                            v-model="signupInfo.last_name.value"
                                            :dense="true"
                                        />
                                    </div>
                                    <div class="flex flex-nowrap items-center justify-start pt-3">
                                        <q-icon
                                            class="opacity-50 pr-1"
                                            size="sm"
                                            name="account_circle"
                                        />
                                        <q-input
                                            class="text-shadow-lg w-full"
                                            type="text"
                                            :placeholder="tran('UserName', store.state.lang)"
                                            standout
                                            v-model="signupInfo.username.value"
                                            :dense="true"
                                        />
                                    </div>
                                    <div class="flex flex-nowrap items-center justify-start pt-3">
                                        <q-icon
                                            class="opacity-50 pr-1"
                                            size="sm"
                                            name="mail"
                                        />
                                        <q-input
                                            class=" text-shadow-lg w-full"
                                            type="email"
                                            :placeholder="tran('Email address', store.state.lang)"
                                            standout
                                            v-model="signupInfo.email.value"
                                            :dense="true"
                                        />
                                    </div>
                                    <div class="flex flex-nowrap items-center justify-start pt-3">
                                        <q-icon
                                            class="opacity-50 pr-1"
                                            size="sm"
                                            name="lock"
                                        />
                                        <q-input
                                            class=" text-shadow-lg w-full"
                                            type="password"
                                            :placeholder="tran('Password', store.state.lang)"
                                            standout
                                            v-model="signupInfo.password.value"
                                            :dense="true"
                                        />
                                    </div>
                                    <div class="flex flex-nowrap items-center justify-start pt-3">
                                        <q-icon
                                            class="opacity-50 pr-1"
                                            size="sm"
                                            name="enhanced_encryption"
                                        />
                                        <q-input
                                            class=" text-shadow-lg w-full"
                                            type="password"
                                            :placeholder="tran('Password Confirmation', store.state.lang)"
                                            standout
                                            v-model="signupInfo.password_confirmation.value"
                                            :dense="true"
                                        />
                                    </div>
                                    <div class="flex flex-nowrap items-center justify-start pt-3">
                                        <q-icon
                                            class="opacity-50 pr-1"
                                            size="sm"
                                            name="euro"
                                        />
                                        <q-btn-dropdown
                                            class="btn-none float-right w-full"
                                            label-class="d-flex flex-nowrap align-items-center"
                                            style="background-color: #1266CD;"
                                            >
                                            <template v-slot:label>
                                                <div
                                                    class="flex flex-nowrap items-center justify-start text-xs font-normal sm:text-sm sm:pr-3"
                                                    >
                                                    {{ signupInfo.currency.value=='' ? tran('Currency', store.state.lang) : signupInfo.currency.value }}
                                                </div>
                                            </template>
                                            <q-list>
                                                <q-item
                                                    v-for="currency in currencys"
                                                    clickable
                                                    v-close-popup
                                                    @click="onCurrencyItemClick(currency)"
                                                    >
                                                    <q-item-section>
                                                        <q-item-label>
                                                            <div class="flex flex-nowrap items-center justify-start">
                                                                <p class="text-xs pl-1">{{ currency }}</p>
                                                            </div>
                                                        </q-item-label>
                                                    </q-item-section>
                                                </q-item>
                                            </q-list>
                                        </q-btn-dropdown>
                                    </div>
                                </q-tab-panel>
                                <q-tab-panel name="Prev">
                                    <div class="flex flex-nowrap items-center justify-start">
                                        <q-icon 
                                            name="event" 
                                            class="cursor-pointer opacity-50"
                                            size="sm"
                                        >
                                        </q-icon>
                                        <q-input
                                            class="text-shadow-lg w-full pl-1"
                                            type="text"
                                            mask="date"
                                            :placeholder="tran('Birthday', store.state.lang)"
                                            standout
                                            v-model="signupInfo.dob.value"
                                            :dense="true"
                                        >
                                            <q-icon 
                                                name="event" 
                                                class="cursor-pointer mt-2"
                                                size="sm"
                                            >
                                                <q-popup-proxy cover transition-show="scale" transition-hide="scale">
                                                    <q-date v-model="signupInfo.dob.value">
                                                        <div class="row items-center justify-end">
                                                            <q-btn v-close-popup :label="tran('Close', store.state.lang)" color="primary" flat />
                                                        </div>
                                                    </q-date>
                                                </q-popup-proxy>
                                            </q-icon>
                                        </q-input>
                                    </div>
                                    <div class="flex flex-nowrap items-center justify-start pt-1">
                                        <q-icon
                                            class="opacity-50 pr-1"
                                            size="sm"
                                            name="wc"
                                        />
                                        <q-btn-dropdown 
                                            class="btn-none w-full"
                                            label-class="d-flex flex-nowrap align-items-center"
                                            style="background-color: #1266CD;"
                                        >
                                            <template v-slot:label>
                                                <div
                                                    class="flex flex-nowrap items-center justify-start text-xs font-normal sm:text-sm sm:pr-3"
                                                >
                                                    {{ tran(signupInfo.gender.value, store.state.lang) }}
                                                </div>
                                            </template>
                                            <q-list>
                                                <q-item
                                                    v-for="gender in genders"
                                                    clickable
                                                    v-close-popup
                                                    @click="onGenderItemClick(gender)"
                                                >
                                                    <q-item-section>
                                                        <q-item-label>
                                                            <div class="flex flex-nowrap items-center justify-start">
                                                                <p class="text-xs pl-1">{{ tran(gender, store.state.lang) }}</p>
                                                            </div>
                                                        </q-item-label>
                                                    </q-item-section>
                                                </q-item>
                                            </q-list>
                                        </q-btn-dropdown>
                                    </div>
                                    <div class="flex flex-nowrap items-center justify-start pt-1">
                                        <q-icon
                                            class="opacity-50 pr-1"
                                            size="sm"
                                            name="language"
                                        />
                                        <q-select
                                            filled
                                            v-model="model"
                                            use-input
                                            :placeholder="tran('Country', store.state.lang)"
                                            hide-selected
                                            input-debounce="0"
                                            :options="countries"
                                            @filter="filterFn"
                                            class="w-full"
                                            :dense="true"
                                        >
                                            <template v-slot:no-option>
                                            <q-item>
                                                <q-item-section class="text-grey">
                                                    {{tran('No results', store.state.lang)}}
                                                </q-item-section>
                                            </q-item>
                                            </template>
                                        </q-select>
                                    </div>
                                    <div class="flex flex-nowrap items-center justify-start">
                                        <q-icon
                                            class="opacity-50 pr-1"
                                            size="sm"
                                            name="flag"
                                        />
                                        <q-input
                                            class="pt-1 text-shadow-lg w-full"
                                            type="text"
                                            :placeholder="tran('State', store.state.lang)"
                                            standout
                                            v-model="signupInfo.state.value"
                                            :dense="true"
                                        />
                                    </div>
                                    <div class="flex flex-nowrap items-center justify-start">
                                        <q-icon
                                            class="opacity-50 pr-1"
                                            size="sm"
                                            name="room"
                                        />
                                        <q-input
                                            class="pt-1 text-shadow-lg w-full"
                                            :placeholder="tran('City', store.state.lang)"
                                            standout
                                            v-model="signupInfo.city.value"
                                            :dense="true"
                                        />
                                    </div>
                                    <div class="flex flex-nowrap items-center justify-start">
                                        <q-icon
                                            class="opacity-50 pr-1"
                                            size="sm"
                                            name="add_road"
                                        />
                                        <q-input
                                            class="pt-1 text-shadow-lg w-full"
                                            type="text"
                                            :placeholder="tran('Street', store.state.lang)"
                                            standout
                                            v-model="signupInfo.street_1_address.value"
                                            :dense="true"
                                        />
                                    </div>
                                    <div class="flex flex-nowrap items-center justify-start pt-1">
                                        <q-icon
                                            class="opacity-50 pr-1"
                                            size="sm"
                                            name="mark_as_unread"
                                        />
                                        <q-input
                                            class="text-shadow-lg w-full"
                                            :placeholder="tran('Postal Code', store.state.lang)"
                                            standout
                                            v-model="signupInfo.zip.value"
                                            :dense="true"
                                        />
                                    </div>
                                    <div class="flex flex-nowrap items-center justify-start">
                                        <q-icon
                                            class="opacity-50 pr-1"
                                            size="sm"
                                            name="phone"
                                        />
                                        <q-input
                                            class="py-1 text-shadow-lg w-full"
                                            :prefix="signupInfo.countryCode.value"
                                            type="tel"
                                            mask="phone"
                                            max-length="10"
                                            placeholder="Mobile Number"
                                            standout
                                            v-model="signupInfo.phone.value"
                                            :dense="true"
                                        />
                                    </div>
                                </q-tab-panel>
                            </q-tab-panels>
                            
                            <div 
                                class="grid flex justify-center"
                                :class="tab=='Prev'&&'grid-cols-2 gap-1'"
                            >
                                <q-btn 
                                    class="w-full font-bold px-11"
                                    style="background-color: #fff004; color: black;" 
                                    :label="tran(tab, store.state.lang)" 
                                    @click="tabChange"/>
                                
                                <q-btn
                                    v-if="tab == 'Prev'"
                                    @click="signUp"
                                    class="font-bold w-full"
                                    style=" 
                                        background-color: #fff004;
                                        color: black;
                                    "
                                    :label="tran('Register', store.state.lang)"
                                />
                            </div>
                        </div>
                    </div>
                </div>
            </div>
        </q-card>
    </q-dialog>
</template>
