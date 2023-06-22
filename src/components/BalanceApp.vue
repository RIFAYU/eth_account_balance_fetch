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
        <form @submit.prevent
        class="flex flex-col items-center mt-6">
            <div class="w-[340px] md:w-[640px] p-2 flex">
                <input type="text" 
                placeholder="Paste wallet address" 
                maxlength="42" 
                v-model="accountaddress"
                class="focus:outline-none border border-gray-300 rounded-sm p-5 flex flex-1 w-28">
                <!-- <select name="" class="border border-gray-300 bg-white">
                    <option>ETH</option>
                    <option value="">Sepolia</option>
                    <option value="">Gorelio</option>
                </select> -->
            </div>
            <button @click="getbalance" 
            class="text-white bg-blue-800 rounded-sm px-3 py-3 mt-6 hover:bg-blue-700">
            Check Balance</button>
        </form>
    </section>
    <!-- card for output -->
    <section class="flex justify-center">
        <div v-show="isshow" class="bg-white p-12 shadow-md">
            <p class="text-gray-500 mb-4">ETH wallet balance</p>
            <div class="">
                <p class="font-bold text-4xl">{{ accountbalance }} ETH</p>
                <p class="">{{ indollar }}</p>
            </div>
            <p class="text-gray-500 mt-4">{{ currentdate }}</p>
        </div>
    </section>


    <!-- history of Balance -->
    <section class="bg-blue-600 mt-16 flex overflow-scroll justify-between rounded-t-md">
        <div v-for="(list,id) in data" :key="id">
            <div class="bg-white m-4 p-12 shadow-md rounded-md">
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
<script>

import {ethers} from "ethers"
const provider = new ethers.providers.JsonRpcProvider(`https://mainnet.infura.io/v3/3f6de7a5e7a8406392733a5ea9ca1d68`);
let fetch = JSON.parse(localStorage.getItem('Items'))

export default {
    data(){
        return {
            accountbalance : 0,
            accountaddress : '',
            isshow: false,
            data: fetch || []
        }
    },
    computed:{
        currentdate(){
            const date = new Date();
            const cusdate = date.toLocaleString('default', { month: 'long' }) + " " + date.getDate() + ", " + date.getFullYear() + " " + date.toLocaleString('en-US', { hour: 'numeric', minute: 'numeric', hour12: true })
            return cusdate;
        },
        indollar(){
            const cost = ` $ ${(this.accountbalance*1907).toPrecision(6)}`
            return cost;
        }
    },
    methods:{
        getbalance(){
            (async () => {try{
                const acbalance = await provider.getBalance(this.accountaddress);
                const getbal = ethers.utils.formatEther(acbalance);
                this.accountbalance = Number(getbal).toPrecision(5);
                this.isshow = true;
                let data = [...this.data,{'accountaddress':this.accountaddress,'date':this.currentdate,'accountblance':this.accountbalance,'priceindollar':this.indollar}]
                localStorage.setItem('Items',JSON.stringify(data))}catch(e){alert("Please Enter a Vaild Address 0x000")}
            })()
        },
        delhis(id){
            this.data.splice(id,1)
            localStorage.setItem('Items',JSON.stringify(this.data))
        }
    }
  
}
</script>
