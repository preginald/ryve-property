<template>
    <div class="p-10">
        <div>
            <h1>RYVE STRATEGIZER</h1>
        </div>
        <label for="entity" class="block mb-2 text-sm font-medium text-gray-900 dark:text-white">Step 1: Select an entity.</label>
        <button @click="toggleIndividualForm" type="button">Individual</button>
        <button @click="toggleIndividualForm" type="button">Company</button>

        <div v-if="showIndividualForm">
            <div class="grid gap-6 mb-6 md:grid-cols-3">
                <div class="mr-5 w-full max-w-sm p-4 bg-white border border-purple-200 rounded-lg shadow sm:p-6 md:p-8 dark:bg-gray-800 dark:border-purple-700">
                    <h5 class="text-xl font-medium text-gray-900 dark:text-white">{{ individualFormData.label }}</h5>
                        <div class="mt-3">
                            <label for="first_name" class="block mb-2 text-sm font-medium text-gray-900 dark:text-white">First name</label>
                            <input type="text" v-model="individualFormData.first_name" id="first_name" placeholder="John" required class="input-rounded">
                        </div>
                        <div class="mt-3">
                            <label for="last_name" class="block mb-2 text-sm font-medium text-gray-900 dark:text-white">Last name</label>
                            <input type="text" v-model="individualFormData.last_name" id="last_name" placeholder="Doe" required class="input-rounded">
                        </div>
                        <div class="mt-3">
                            <label for="phone" class="block mb-2 text-sm font-medium text-gray-900 dark:text-white">Phone number</label>
                            <input type="tel" v-model="individualFormData.phone_number" id="phone" placeholder="123-45-678" pattern="[0-9]{3}-[0-9]{2}-[0-9]{3}" required class="input-rounded">
                        </div>
                        <div class="mt-3">
                            <label for="email" class="block mb-2 text-sm font-medium text-gray-900 dark:text-white">Email address</label>
                            <input type="email" v-model="individualFormData.email" id="email" placeholder="john.doe@company.com" required class="input-rounded">
                        </div>
                    <div class="flex items-start my-6">
                        <div class="flex items-center h-5">
                        <input id="remember" v-model="individualFormData.compliance" type="checkbox" value="" class="w-4 h-4 border border-gray-300 rounded bg-gray-50 focus:ring-3 focus:ring-blue-300 dark:bg-gray-700 dark:border-gray-600 dark:focus:ring-blue-600 dark:ring-offset-gray-800" required>
                        </div>
                        <label for="remember" class="ml-2 text-sm font-medium text-gray-900 dark:text-gray-300">I agree with the <a href="#" class="text-blue-600 hover:underline dark:text-blue-500">terms and conditions</a>.</label>
                    </div>
                    <button @click="addToEntities" type="button" class="mt-5">Add Individual</button>
                </div>
            </div>
        </div>
        
        <div v-if="entities.length > 0" class="mt-10">
            <h2>Entities</h2>
            <div class="grid gap-6 mb-6 md:grid-cols-3">
                <div v-for="entity in entities" class="my-5 mr-5 w-full max-w-sm p-4 bg-white border border-gray-200 rounded-lg shadow sm:p-6 md:p-8 dark:bg-gray-800 dark:border-gray-700">
                    <h5 class="text-xl font-medium text-gray-900 dark:text-white">{{ entity.label }}</h5>
                    <div v-if="entity.type==='individual'" class="grid gap-6 mb-6 md:grid-cols-2">
                        <div>
                            <label for="first_name" class="block mb-2 text-sm font-medium text-gray-900 dark:text-white">First name</label>
                            <input v-model="entity.first_name" type="text" id="first_name" placeholder="John" required class="input-rounded">
                        </div>
                        <div>
                            <label for="last_name" class="block mb-2 text-sm font-medium text-gray-900 dark:text-white">Last name</label>
                            <input v-model="entity.last_name" type="text" id="last_name" placeholder="Doe" required class="input-rounded">
                        </div>
                        <div>
                            <label for="phone" class="block mb-2 text-sm font-medium text-gray-900 dark:text-white">Phone number</label>
                            <input v-model="entity.phone_number" type="tel" id="phone" placeholder="123-45-678" pattern="[0-9]{3}-[0-9]{2}-[0-9]{3}" required class="input-rounded">
                        </div>
                    </div>
                    <div class="mb-6">
                        <label for="email" class="block mb-2 text-sm font-medium text-gray-900 dark:text-white">Email address</label>
                        <input v-model="entity.email" type="email" id="email" placeholder="john.doe@company.com" required class="input-rounded">
                    </div> 
                    <div class="mb-6">
                        <label for="salary" class="block mb-2 text-sm font-medium text-gray-900 dark:text-white">Salary {{ yearlySalary(entity) }}</label>
                        <div class="flex">
                            <input v-model="entity.salary" type="number" id="salary" placeholder="100,000" required class="input-rounded-l">
                            <select v-model="entity.salary_period" class="select-rounded-r">
                                <option 
                                    v-for="period in periods">
                                    {{ period.label }}
                                </option>
                            </select>
                        </div>
                    </div>
                </div>
            </div>
        </div>
        
        <div v-if="entities.length > 0">
            <label for="assets" class="block mb-2 text-sm font-medium text-gray-900 dark:text-white">What is your first type of asset?</label>
            <select v-model="selectedAsset" class="select-rounded">
                <option 
                v-for="asset in assetTypes" 
                :value="asset">
                {{ asset.label }}
                </option>
            </select>

            <button @click="addToAssets" type="button" class="mt-5">Add Asset</button>
            
        </div>
        <div v-if="assets.length > 0">
            <h2 class="mt-10">My portfolio</h2>
            <div class="grid grid-cols-4 gap-4 mt-10">
                <div v-for="asset in assets">

                    <div v-if="asset.type === 'savings'" class="w-full max-w-sm p-4 bg-white border border-gray-200 rounded-lg shadow sm:p-6 md:p-8 dark:bg-gray-800 dark:border-gray-700">
                        <div class="space-y-6">
                            <h5 class="text-xl font-medium text-gray-900 dark:text-white">{{ asset.label }}</h5>
                            <div>
                                <label for="label" class="block mb-2 text-sm font-medium text-gray-900 dark:text-white">Savings label</label>
                                <input v-model="asset.label" type="text" name="label" id="label" required class="select-rounded">
                            </div>
                            <div>
                                <label for="interest_rate" class="block mb-2 text-sm font-medium text-gray-900 dark:text-white">Interest rate</label>
                                <input v-model="asset.interest_rate" type="number" name="interest_rate" id="interest_rate" required class="select-rounded">
                            </div>
                            <div>
                                <label for="total_value" class="block mb-2 text-sm font-medium text-gray-900 dark:text-white">Total value</label>
                                <input v-model="asset.total_value" type="number" name="total_value" id="total_value" required class="select-rounded">
                            </div>
                            <div>
                                <label for="income" class="block mb-2 text-sm font-medium text-gray-900 dark:text-white">Income {{ yearlyIncome(asset) }}</label>
                                <div class="flex">
                                    <input v-model="asset.income" type="number" name="rental_income" id="rental_income" class="input-rounded-l" required>
                                    <select v-model="asset.income_period" class="select-rounded-r">
                                        <option 
                                            v-for="period in periods">
                                            {{ period.label }}
                                        </option>
                                    </select>
                                </div>
                            </div>
                        </div>
                    </div>

                    <div v-if="asset.type === 'stocks'" class="w-full max-w-sm p-4 bg-white border border-gray-200 rounded-lg shadow sm:p-6 md:p-8 dark:bg-gray-800 dark:border-gray-700">
                        <div class="space-y-6">
                            <h5 class="text-xl font-medium text-gray-900 dark:text-white">{{ asset.label }}</h5>
                            <div>
                                <label for="label" class="block mb-2 text-sm font-medium text-gray-900 dark:text-white">Stocks label</label>
                                <input v-model="asset.label" type="text" name="label" id="label" required class="select-rounded">
                            </div>
                            <div>
                                <label for="dividend_rate" class="block mb-2 text-sm font-medium text-gray-900 dark:text-white">Dividend rate</label>
                                <input v-model="asset.dividend_rate" type="number" name="dividend_rate" id="dividend_rate" required class="input-rounded">
                            </div>
                            <div>
                                <label for="total_value" class="block mb-2 text-sm font-medium text-gray-900 dark:text-white">Total value</label>
                                <input v-model="asset.total_value" type="number" name="total_value" id="total_value" class="bg-gray-50 border border-gray-300 text-gray-900 text-sm rounded-lg focus:ring-blue-500 focus:border-blue-500 block w-full p-2.5 dark:bg-gray-600 dark:border-gray-500 dark:placeholder-gray-400 dark:text-white" required>
                            </div>
                            <div>
                                <label for="income" class="block mb-2 text-sm font-medium text-gray-900 dark:text-white">Income {{ yearlyIncome(asset) }}</label>
                                <div class="flex">
                                    <input v-model="asset.income" type="number" name="rental_income" id="rental_income" class="input-rounded-l" required>
                                    <select v-model="asset.income_period" class="select-rounded-r">
                                        <option 
                                            v-for="period in periods">
                                            {{ period.label }}
                                        </option>
                                    </select>
                                </div>
                            </div>
                        </div>
                    </div>

                    <div v-if="asset.type === 'real_estate_commercial'" class="w-full max-w-sm p-4 bg-white border border-gray-200 rounded-lg shadow sm:p-6 md:p-8 dark:bg-gray-800 dark:border-gray-700">
                        <div class="space-y-6">
                            <h5 class="text-xl font-medium text-gray-900 dark:text-white">{{ asset.label }}</h5>
                            <div>
                                <label for="label" class="block mb-2 text-sm font-medium text-gray-900 dark:text-white">Commercial Real Estate label</label>
                                <input v-model="asset.label" type="text" name="label" id="label" required class="select-rounded">
                            </div>
                            <div>
                                <label for="total_value" class="block mb-2 text-sm font-medium text-gray-900 dark:text-white">Total value</label>
                                <input v-model="asset.total_value" type="number" name="total_value" id="total_value" class="bg-gray-50 border border-gray-300 text-gray-900 text-sm rounded-lg focus:ring-blue-500 focus:border-blue-500 block w-full p-2.5 dark:bg-gray-600 dark:border-gray-500 dark:placeholder-gray-400 dark:text-white" required>
                            </div>
                            <div>
                                <label for="income" class="block mb-2 text-sm font-medium text-gray-900 dark:text-white">Income {{ yearlyIncome(asset) }}</label>
                                <div class="flex">
                                    <input v-model="asset.income" type="number" name="rental_income" id="rental_income" class="input-rounded-l" required>
                                    <select v-model="asset.income_period" class="select-rounded-r">
                                        <option 
                                            v-for="period in periods">
                                            {{ period.label }}
                                        </option>
                                    </select>
                                </div>
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
                                <label for="label" class="block mb-2 text-sm font-medium text-gray-900 dark:text-white">Residential Real Estate label</label>
                                <input v-model="asset.label" type="text" name="label" id="label" required class="select-rounded">
                            </div>
                            <div>
                                <label for="total_value" class="block mb-2 text-sm font-medium text-gray-900 dark:text-white">Total value</label>
                                <input v-model="asset.total_value" type="number" name="total_value" id="total_value" class="bg-gray-50 border border-gray-300 text-gray-900 text-sm rounded-lg focus:ring-blue-500 focus:border-blue-500 block w-full p-2.5 dark:bg-gray-600 dark:border-gray-500 dark:placeholder-gray-400 dark:text-white" required>
                            </div>
                            <div>
                                <label for="rental_income" class="block mb-2 text-sm font-medium text-gray-900 dark:text-white">Income {{ yearlyIncome(asset) }}</label>
                                <div class="flex">
                                    <input v-model="asset.income" type="number" name="rental_income" id="rental_income" class="input-rounded-l" required>
                                    <select v-model="asset.income_period" class="select-rounded-r">
                                        <option 
                                            v-for="period in periods">
                                            {{ period.label }}
                                        </option>
                                    </select>
                                </div>
                            </div>
                            <div>
                                <label for="expense" class="block mb-2 text-sm font-medium text-gray-900 dark:text-white">Expense</label>
                                <input v-model="asset.expense" type="number" name="expense" id="expense" class="bg-gray-50 border border-gray-300 text-gray-900 text-sm rounded-lg focus:ring-blue-500 focus:border-blue-500 block w-full p-2.5 dark:bg-gray-600 dark:border-gray-500 dark:placeholder-gray-400 dark:text-white" required>
                            </div>
                        </div>
                    </div>

                    <div v-if="asset.type === 'business'" class="w-full max-w-sm p-4 bg-white border border-gray-200 rounded-lg shadow sm:p-6 md:p-8 dark:bg-gray-800 dark:border-gray-700">
                        <div class="space-y-6">
                            <h5 class="text-xl font-medium text-gray-900 dark:text-white">{{ asset.label }}</h5>
                            <div>
                                <label for="label" class="block mb-2 text-sm font-medium text-gray-900 dark:text-white">Business label</label>
                                <input v-model="asset.label" type="text" name="label" id="label" required class="select-rounded">
                            </div>
                            <div>
                                <label for="total_value" class="block mb-2 text-sm font-medium text-gray-900 dark:text-white">Total value</label>
                                <input v-model="asset.total_value" type="number" name="total_value" id="total_value" class="bg-gray-50 border border-gray-300 text-gray-900 text-sm rounded-lg focus:ring-blue-500 focus:border-blue-500 block w-full p-2.5 dark:bg-gray-600 dark:border-gray-500 dark:placeholder-gray-400 dark:text-white" required>
                            </div>
                            <div>
                                <label for="rental_income" class="block mb-2 text-sm font-medium text-gray-900 dark:text-white">Income {{ yearlyIncome(asset) }}</label>
                                <div class="flex">
                                    <input v-model="asset.income" type="number" name="rental_income" id="rental_income" class="input-rounded-l" required>
                                    <select v-model="asset.income_period" class="select-rounded-r">
                                        <option 
                                            v-for="period in periods">
                                            {{ period.label }}
                                        </option>
                                    </select>
                                </div>
                            </div>
                            <div>
                                <label for="expense" class="block mb-2 text-sm font-medium text-gray-900 dark:text-white">Expense {{ convertToAnnual(asset.expense, asset.expense_period) }}</label>
                                <div class="flex">
                                    <input v-model="asset.expense" type="number" name="expense" id="expense" class="input-rounded-l" required>
                                    <select v-model="asset.expense_period" class="select-rounded-r">
                                        <option 
                                            v-for="period in periods">
                                            {{ period.label }}
                                        </option>
                                    </select>

                                </div>
                            </div>
                        </div>
                    </div>
                </div>

            </div>

        </div>

        <div class="mt-10" v-if="showDebug">
            <h4>Debug</h4>
            <div class="text-slate-500">
                <p>
                    Individual form: {{ showIndividualForm }}
                </p>
                <p>
                    Individual form data: {{ individualFormData }}
                </p>
                <p>
                    Entities: {{ entities }}
                </p>
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

let showIndividualForm = ref(false)
let entities = ref([]);
let assets = ref([]);
let selectedAsset = {};  
let showDebug = true;

let individualFormData = {
    type: "individual", 
    label: "Individual",
    first_name: "",
    last_name: "",
    phone_number: "",
    email: "",
    compliance: false,
    salary: 0,
    salary_period: 'Yearly',
}

const periods = [
    {value: "weekly", label: "Weekly"},
    {value: "fortnightly", label: "Fortnightly"},
    {value: "monthly", label: "Monthly"},
    {value: "yearly", label: "Yearly"},
]

const toggleIndividualForm = () => {
    showIndividualForm.value = !showIndividualForm.value
}

const addToEntities = () => {
    // clone individualFormData and push it to entities
    entities.value.push({...individualFormData})
    closeIndividualForm()
}

const convertToAnnual = (value: Number,period: String) => {
    let multiplier = 0

    switch (period) {
        case "Weekly":
            multiplier = 52
            break;
    
        case "Fortnightly":
            multiplier = 26 
            break;
    
        case "Monthly":
            multiplier = 12 
            break;
        
        case "Yearly":
            multiplier = 1
    }

    if(value > 0 && period){
        
        value = value * multiplier 
        return value.toString().replace(/\D/g, "").replace(/\B(?=(\d{3})+(?!\d))/g, ",") + "/pa";
    }
}

const yearlySalary = (entity) => {
    let multiplier = 0
    console.log(entity.salary_period)
    switch (entity.salary_period) {
        case "Weekly":
            multiplier = 52
            break;
    
        case "Fortnightly":
            multiplier = 26 
            break;
    
        case "Monthly":
            multiplier = 12 
            break;
        
        case "Yearly":
            multiplier = 1
    }

    if(entity.salary > 0 && entity.salary_period){
        
        let salary = entity.salary * multiplier 
        return salary.toString().replace(/\D/g, "").replace(/\B(?=(\d{3})+(?!\d))/g, ",") + "/pa";
    }
}

const yearlyIncome = (asset) => {
    let multiplier = 0
    switch (asset.income_period) {
        case "Weekly":
            multiplier = 52
            break;
    
        case "Fortnightly":
            multiplier = 26 
            break;
    
        case "Monthly":
            multiplier = 12 
            break;
        
        case "Yearly":
            multiplier = 1
    }

    if(asset.income > 0 && asset.income_period){
        
        let income = asset.income * multiplier 
        return income.toString().replace(/\D/g, "").replace(/\B(?=(\d{3})+(?!\d))/g, ",") + "/pa";
    }
}

const closeIndividualForm = () => {
    showIndividualForm.value = false
    individualFormData.first_name = ""
    individualFormData.last_name = ""
    individualFormData.phone_number = ""
    individualFormData.email = ""
    individualFormData.compliance = false
}

const addToAssets = () => {
    assets.value.push({...selectedAsset})
    selectedAsset = ''
}

let assetTypes = [
  {type: "savings", label: "Savings", interest_rate: 0.5, total_value: 2000, income: 100, income_period: "Yearly"},
  {type: "stocks", label: "Stocks", dividend_rate: 4.5, total_value: 5000, income: 225, income_period: "Yearly"},
  {type: "real_estate_commercial", label: "Commercial Real Estate", income: 9000, expense: 1000, total_value: 1000000,  income_period: "Monthly", expense_period: "Monthly"},
  {type: "real_estate_residential", label: "Residential Real Estate", income: 666, expense: 1000, total_value: 1000000, income_period: "Weekly", expense_period: "Monthly"},
  {type: "collectibles", label: "Collectibles", total_value: 1000},
  {type: "eft", label: "EFT", dividend_rate: 4.5, total_value: 5000, income: 225, income_period: "Weekly"},
  {type: "bonds", label: "Bonds", dividend_rate: 4.1, total_value: 10000, income: 410, income_period: "Weekly"},
  {type: "mutual_funds", label: "Mutual Funds", dividend_rate: 4.5, total_value: 5000, income: 225, income_period: "Weekly"},
  {type: "term_deposits", label: "Term Deposits", dividend_rate: 5.2, total_value: 50000, income: 2600, income_period: "Weekly"},
  {type: "business", label: "Business", income: 6000, income_period: "Monthly", expense: 3000, expense_period: "Monthly", total_value: 50000},
]



</script>

<style scoped>
</style>