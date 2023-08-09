<script setup>

import { ethers } from "ethers"
import { reactive, computed, onBeforeUpdate } from 'vue'

let fetch = JSON.parse(localStorage.getItem('Items'))

/* Reactive State */

const state = reactive({
    accountbalance: 0,
    accountaddress: '',
    isshow: false,
    option: '',
    data: fetch || []
})

/* Coumputed Properties */

const currentdate = computed(() => {
    const date = new Date();
    const cusdate = date.toLocaleString('default', { month: 'long' }) + " " + date.getDate() + ", " + date.getFullYear() + " " + date.toLocaleString('en-US', { hour: 'numeric', minute: 'numeric', hour12: true })
    return cusdate;
})

const indollar = computed(() => {
    const cost = ` $ ${(state.accountbalance * 1907).toPrecision(6)}`
    return cost;
})


/* Life Hooks */

onBeforeUpdate(()=>{
    localStorage.setItem('Items', JSON.stringify(state.data))
})

/* Methods */

const delhis = (id) => {
    state.data.splice(id, 1)
}

const getbalance = async () => {
    try {
        const provider = new ethers.providers.JsonRpcProvider(`https://${state.option}.infura.io/v3/your_api_key`);
        const acbalance = await provider.getBalance(state.accountaddress);
        const getbal = ethers.utils.formatEther(acbalance);
        state.accountbalance = Number(getbal).toPrecision(5);
        state.accountaddress = '';
        state.isshow = true;
        state.data = [...state.data, { 'accountaddress': state.accountaddress, 'date': currentdate, 'accountblance': state.accountbalance, 'priceindollar': state.indollar, 'network': state.option }]
    } catch (e) {
        alert("Please Enter a Vaild Address !!!")
    }
}

</script>


<template>
    <main>
        <section class="px-6 py-20 font-sans">
            <header class="flex flex-col items-center">
                <section class="">
                    <h1 class="font-bold text-5xl p-3">Ethereum Balance</h1>
                </section>
                <section class="flex justify-center">
                    <p class=" text-[#8694a4]">Enter public address to check Ethereum wallet balance</p>
                </section>
            </header>
            <form @submit.prevent class="flex flex-col items-center mt-6">
                <div class="w-[340px] md:w-[640px] p-2 flex">
                    <input type="text" placeholder="Paste wallet address" v-model.trim="state.accountaddress"
                        class="focus:outline-none border border-gray-300 rounded-sm p-5 flex flex-1 w-28">
                    <!-- selecting a network -->
                    <select name="" id="" v-model="state.option" class="border border-gray-300 bg-white text-center">
                        <option value="" hidden>Select Network</option>
                        <option value="mainnet">ETH</option>
                        <option value="sepolia">SEPOLIA (TN)</option>
                        <option value="goerli">GOERLI (TN)</option>
                        <option value="palm-mainnet">PALM</option>
                        <option value="palm-testnet">PALM (TN)</option>
                        <option value="aurora-mainnet">AURORA</option>
                        <option value="aurora-testnet">AURORA (TN)</option>
                        <option value="near-mainnet">NEAR</option>
                        <option value="near-testnet">NEAR (TN)</option>
                        <option value="stark-mainnet">STARK</option>
                        <option value="celo-mainnet">CELO</option>
                    </select>
                </div>
                <button @click="getbalance" class="text-white bg-blue-800 rounded-sm px-3 py-3 mt-6 hover:bg-blue-700">
                    Check Balance</button>
            </form>
        </section>
        <!-- card for output -->
        <section class="flex justify-center">
            <div v-show="state.isshow" class="bg-white p-12 shadow-md">
                <p class="text-gray-500 mb-4">ETH wallet balance</p>
                <div class="">
                    <p class="font-bold text-4xl">{{ state.accountbalance }} ETH</p>
                    <p class="">{{ indollar }}</p>
                </div>
                <p class="text-gray-500 mt-4">{{ currentdate }}</p>
            </div>
        </section>


        <!-- history of Balance -->
        <section class="bg-blue-600 mt-16 flex overflow-scroll justify-center rounded-t-md">
            <div v-for="(list, id) in state.data" :key="id">
                <div class="bg-white m-4 p-12 shadow-md rounded-md">
                    <p class=""><b>Network Name : </b> {{ list.network }}</p>
                    <section class="flex justify-between mt-0">
                        <p class="text-gray-500 mb-4">ETH wallet balance History</p>
                        <button class="font-bold hover:bg-red-500 p-1 rounded-md shadow-md text-white bg-red-600"
                            @click="delhis(id)">DELETE</button>
                    </section>
                    <p class="mb-4"><b>Wallet Address : </b>{{ list.accountaddress }}</p>
                    <div class="">
                        <p class="font-bold text-4xl">{{ list.accountblance }} ETH</p>
                        <p class="">{{ list.priceindollar }}</p>
                    </div>
                    <p class="text-gray-500 mt-4">{{ list.date }}</p>
                </div>
            </div>
        </section>

    </main>
</template>
