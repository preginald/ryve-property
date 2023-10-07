<template>
    <div class="p-10">
        <div>
            <h1>Ryve Investment Strategiser</h1>
        </div>
        <div class="grid gap-6 mb-6 md:grid-cols-2">
            <div>
                <label for="first_name" class="block mb-2 text-sm font-medium text-gray-900 dark:text-white">First name</label>
                <input type="text" id="first_name" placeholder="John" required>
            </div>
            <div>
                <label for="last_name" class="block mb-2 text-sm font-medium text-gray-900 dark:text-white">Last name</label>
                <input type="text" id="last_name" placeholder="Doe" required>
            </div>
            <div>
                <label for="company" class="block mb-2 text-sm font-medium text-gray-900 dark:text-white">Company</label>
                <input type="text" id="company" placeholder="Flowbite" required>
            </div>  
            <div>
                <label for="phone" class="block mb-2 text-sm font-medium text-gray-900 dark:text-white">Phone number</label>
                <input type="tel" id="phone" placeholder="123-45-678" pattern="[0-9]{3}-[0-9]{2}-[0-9]{3}" required>
            </div>
        </div>
        <div class="mb-6">
            <label for="email" class="block mb-2 text-sm font-medium text-gray-900 dark:text-white">Email address</label>
            <input type="email" id="email" placeholder="john.doe@company.com" required>
        </div> 
        <div class="flex items-start mb-6">
            <div class="flex items-center h-5">
            <input id="remember" type="checkbox" value="" class="w-4 h-4 border border-gray-300 rounded bg-gray-50 focus:ring-3 focus:ring-blue-300 dark:bg-gray-700 dark:border-gray-600 dark:focus:ring-blue-600 dark:ring-offset-gray-800" required>
            </div>
            <label for="remember" class="ml-2 text-sm font-medium text-gray-900 dark:text-gray-300">I agree with the <a href="#" class="text-blue-600 hover:underline dark:text-blue-500">terms and conditions</a>.</label>
        </div>
        <div>
            <label for="assets" class="block mb-2 text-sm font-medium text-gray-900 dark:text-white">What is your first type of asset?</label>
            <select v-model="selectedAsset">
                <option 
                v-for="asset in assetTypes" 
                :value="asset">
                {{ asset.label }}
                </option>
            </select>

            <button @click="addToAssets" type="button" class="mt-5">Add to portfolio!</button>
            
        </div>
        <div v-if="assets.length > 0">
            <h2 class="mt-10">My portfolio</h2>
            <div class="grid grid-cols-4 gap-4 mt-10">
                <div v-for="asset in assets">

                    <div v-if="asset.type === 'savings'" class="w-full max-w-sm p-4 bg-white border border-gray-200 rounded-lg shadow sm:p-6 md:p-8 dark:bg-gray-800 dark:border-gray-700">
                        <div class="space-y-6">
                            <h5 class="text-xl font-medium text-gray-900 dark:text-white">{{ asset.label }}</h5>
                            <div>
                                <label for="interest_rate" class="block mb-2 text-sm font-medium text-gray-900 dark:text-white">Interest rate</label>
                                <input v-model="asset.interest_rate" type="number" name="interest_rate" id="interest_rate" required>
                            </div>
                            <div>
                                <label for="total_value" class="block mb-2 text-sm font-medium text-gray-900 dark:text-white">Total value</label>
                                <input v-model="asset.total_value" type="number" name="total_value" id="total_value" required>
                            </div>
                            <div>
                                <label for="income" class="block mb-2 text-sm font-medium text-gray-900 dark:text-white">Income</label>
                                <input v-model="asset.income" type="number" name="income" id="income" required>
                            </div>
                        </div>
                    </div>

                    <div v-if="asset.type === 'stocks'">
                        <div class="space-y-6">
                            <h5 class="text-xl font-medium text-gray-900 dark:text-white">{{ asset.label }}</h5>
                            <div>
                                <label for="dividend_rate">Dividend rate</label>
                                <input v-model="asset.dividend_rate" type="number" name="dividend_rate" id="dividend_rate" required>
                            </div>
                            <div>
                                <label for="total_value" class="block mb-2 text-sm font-medium text-gray-900 dark:text-white">Total value</label>
                                <input v-model="asset.total_value" type="number" name="total_value" id="total_value" class="bg-gray-50 border border-gray-300 text-gray-900 text-sm rounded-lg focus:ring-blue-500 focus:border-blue-500 block w-full p-2.5 dark:bg-gray-600 dark:border-gray-500 dark:placeholder-gray-400 dark:text-white" required>
                            </div>
                            <div>
                                <label for="income" class="block mb-2 text-sm font-medium text-gray-900 dark:text-white">Income</label>
                                <input v-model="asset.income" type="number" name="income" id="income" class="bg-gray-50 border border-gray-300 text-gray-900 text-sm rounded-lg focus:ring-blue-500 focus:border-blue-500 block w-full p-2.5 dark:bg-gray-600 dark:border-gray-500 dark:placeholder-gray-400 dark:text-white" required>
                            </div>
                        </div>
                    </div>

                    <div v-if="asset.type === 'real_estate_commercial'" class="w-full max-w-sm p-4 bg-white border border-gray-200 rounded-lg shadow sm:p-6 md:p-8 dark:bg-gray-800 dark:border-gray-700">
                        <div class="space-y-6">
                            <h5 class="text-xl font-medium text-gray-900 dark:text-white">{{ asset.label }}</h5>
                            <div>
                                <label for="total_value" class="block mb-2 text-sm font-medium text-gray-900 dark:text-white">Total value</label>
                                <input v-model="asset.total_value" type="number" name="total_value" id="total_value" class="bg-gray-50 border border-gray-300 text-gray-900 text-sm rounded-lg focus:ring-blue-500 focus:border-blue-500 block w-full p-2.5 dark:bg-gray-600 dark:border-gray-500 dark:placeholder-gray-400 dark:text-white" required>
                            </div>
                            <div>
                                <label for="income" class="block mb-2 text-sm font-medium text-gray-900 dark:text-white">Income</label>
                                <input v-model="asset.income" type="number" name="income" id="income" class="bg-gray-50 border border-gray-300 text-gray-900 text-sm rounded-lg focus:ring-blue-500 focus:border-blue-500 block w-full p-2.5 dark:bg-gray-600 dark:border-gray-500 dark:placeholder-gray-400 dark:text-white" required>
                            </div>
                            <div>
                                <label for="expense" class="block mb-2 text-sm font-medium text-gray-900 dark:text-white">Expense</label>
                                <input v-model="asset.expense" type="number" name="expense" id="expense" class="bg-gray-50 border border-gray-300 text-gray-900 text-sm rounded-lg focus:ring-blue-500 focus:border-blue-500 block w-full p-2.5 dark:bg-gray-600 dark:border-gray-500 dark:placeholder-gray-400 dark:text-white" required>
                            </div>
                        </div>
                    </div>

                    <div v-if="asset.type === 'real_estate_residential'" class="w-full max-w-sm p-4 bg-white border border-gray-200 rounded-lg shadow sm:p-6 md:p-8 dark:bg-gray-800 dark:border-gray-700">
                        <div class="space-y-6">
                            <h5 class="text-xl font-medium text-gray-900 dark:text-white">{{ asset.label }}</h5>
                            <div>
                                <label for="total_value" class="block mb-2 text-sm font-medium text-gray-900 dark:text-white">Total value</label>
                                <input v-model="asset.total_value" type="number" name="total_value" id="total_value" class="bg-gray-50 border border-gray-300 text-gray-900 text-sm rounded-lg focus:ring-blue-500 focus:border-blue-500 block w-full p-2.5 dark:bg-gray-600 dark:border-gray-500 dark:placeholder-gray-400 dark:text-white" required>
                            </div>
                            <div>
                                <label for="rental_income" class="block mb-2 text-sm font-medium text-gray-900 dark:text-white">Income</label>
                                <input v-model="asset.income" type="number" name="rental_income" id="rental_income" class="bg-gray-50 border border-gray-300 text-gray-900 text-sm rounded-lg focus:ring-blue-500 focus:border-blue-500 block w-full p-2.5 dark:bg-gray-600 dark:border-gray-500 dark:placeholder-gray-400 dark:text-white" required>
                            </div>
                        </div>
                    </div>
                </div>

            </div>

        </div>

        <div class="mt-10">
            <h4>Debug</h4>
            <div>
                <p>
                    assets: {{ assets }}
                </p>
                <p>
                    Selected asset: {{ selectedAsset }}
                </p>
            </div>
        </div>
    </div>
</template>

<script setup lang="ts">
import { ref } from 'vue';

let assets = ref([]);
let selectedAsset = {};  

const addToAssets = () => {
    assets.value.push(selectedAsset)
    selectedAsset = ''
}

let assetTypes = [
  {type: "savings", label: "Savings", interest_rate: 0.5, total_value: 2000, income: 100},
  {type: "stocks", label: "Stocks", dividend_rate: 4.5, total_value: 5000, income: 225},
  {type: "real_estate_commercial", label: "Commercial Real Estate", income: 9000, expense: 1000, total_value: 1000000},
  {type: "real_estate_residential", label: "Residential Real Estate", income: 5600, expense: 1000, total_value: 1000000},
  {type: "collectibles", label: "Collectibles", total_value: 1000},
  {type: "eft", label: "EFT", dividend_rate: 4.5, total_value: 5000, income: 225},
  {type: "bonds", label: "Bonds", dividend_rate: 4.1, total_value: 10000, income: 410},
  {type: "mutual_funds", label: "Mutual Funds", dividend_rate: 4.5, total_value: 5000, income: 225},
  {type: "term_deposits", label: "Term Deposits", dividend_rate: 5.2, total_value: 50000, income: 2600},
  {type: "business", label: "Business", income: 6000, expense: 3000, total_value: 50000},
]

</script>

<style scoped>
</style>