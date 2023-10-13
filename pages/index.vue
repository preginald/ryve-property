<template>
    <div class="p-10">
        <div>
            <h1>RYVE STRATEGIZER</h1>
        </div>
        <div class="my-3">
            <h2>Dashboard</h2>
            <div class="flex">
                <div v-if="incomeTotal > 0" class="my-5 mr-5">
                    <Currency title="Income" :value="convertToDollars(incomeTotal)" />           
                </div>
                <div v-if="expenseTotal > 0" class="my-5 mr-5">
                    <Currency title="Expense" :value="convertToDollars(expenseTotal)" />           
                </div>
                <div v-if="incomeTotal > 0 && expenseTotal > 0" class="my-5 mr-5">
                    <Currency title="Cashflow" :value="convertToDollars(incomeTotal - expenseTotal)" />           
                </div>
            </div>
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
                        <label for="salary" class="block mb-2 text-sm font-medium text-gray-900 dark:text-white">Salary {{ convertToAnnual(entity.salary.value, entity.salary.period) }}</label>
                        <div class="flex">
                            <input @change="sumIncome()" v-model="entity.salary.value" type="number" id="salary" placeholder="100,000" required class="input-rounded-l">
                            <select @change="sumIncome()" v-model="entity.salary.period" class="select-rounded-r">
                                <option :value="period.value"
                                    v-for="period in periods">
                                    {{ period.label }}
                                </option>
                            </select>
                        </div>
                    </div>
                </div>
            </div>
        </div>

        <div v-if="entities.length > 0" class="mt-10">
            <div class="">
                <div class="">
                    <h2>Expense - {{ expenseTotal > 0 ? "$" + expenseTotal : "" }}</h2>
                    <div class="grid gap-6 mb-6 md:grid-cols-5">
                        <div>
                            <h3>Residence</h3>
                            <div class="my-3">
                                <label for="Rent" class="block mb-2 text-sm font-medium text-gray-900 dark:text-white">Is your primary residence rented or mortgaged?</label>
                                    <select @change="sumExpenses()" v-model="expense.residence.type" class="select-rounded">
                                        <option value="rent">
                                            Rent
                                        </option>
                                        <option value="mortgage">
                                            Mortgage
                                        </option>
                                    </select>
                            </div>
                            <div v-if="expense.residence.type ==='rent'" class="my-3">
                                <label for="Rent" class="block mb-2 text-sm font-medium text-gray-900 dark:text-white">Rent {{ convertToAnnual(expense.rent.value, expense.rent.period) }}</label>
                                <div class="flex">
                                    <input @change="sumExpenses()" v-model="expense.rent.value" type="number" id="rent" placeholder="100,000" required class="input-rounded-l">
                                    <select v-model="expense.rent.period" class="select-rounded-r">
                                        <option :value="period.value" 
                                            v-for="period in periods">
                                            {{ period.label }}
                                        </option>
                                    </select>
                                </div>
                            </div>
                            <div v-if="expense.residence.type ==='mortgage'" class="my-3">
                                <div class="my-3">
                                    <label for="Mortgage" class="block mb-2 text-sm font-medium text-gray-900 dark:text-white">Morgage {{ convertToAnnual(expense.mortgage.value, expense.mortgage.period) }}</label>
                                    <div class="flex">
                                        <input @change="sumExpenses()" v-model="expense.mortgage.value" type="number" id="mortgage" placeholder="3,200" required class="input-rounded-l">
                                        <select @change="sumExpenses()" v-model="expense.mortgage.period" class="select-rounded-r">
                                            <option :value="period.value"
                                                v-for="period in periods">
                                                {{ period.label }}
                                            </option>
                                        </select>
                                    </div>
                                </div>
                                <div class="my-3">
                                    <label for="Rates" class="block mb-2 text-sm font-medium text-gray-900 dark:text-white">Rates {{ convertToAnnual(expense.rates.value, expense.rates.period) }}</label>
                                    <div class="flex">
                                        <input @change="sumExpenses()" v-model="expense.rates.value" type="number" id="rates" placeholder="3,200" required class="input-rounded-l">
                                        <select @change="sumExpenses()" v-model="expense.rates.period" class="select-rounded-r">
                                            <option :value="period.value"
                                                v-for="period in periods">
                                                {{ period.label }}
                                            </option>
                                        </select>
                                    </div>
                                </div>
                                <div class="my-3">
                                    <label for="bodycorporate" class="block mb-2 text-sm font-medium text-gray-900 dark:text-white">Body corporate {{ convertToAnnual(expense.bodycorporate.value, expense.bodycorporate.period) }}</label>
                                    <div class="flex">
                                        <input @change="sumExpenses()" v-model="expense.bodycorporate.value" type="number" id="bodycorporate" placeholder="3,200" required class="input-rounded-l">
                                        <select @change="sumExpenses()" v-model="expense.bodycorporate.period" class="select-rounded-r">
                                            <option :value="period.value"
                                                v-for="period in periods">
                                                {{ period.label }}
                                            </option>
                                        </select>
                                    </div>
                                </div>
                            </div>
                                <div class="my-3">
                                    <label for="insurance" class="block mb-2 text-sm font-medium text-gray-900 dark:text-white">Home & contents insurance {{ convertToAnnual(expense.insurance.value, expense.insurance.period) }}</label>
                                    <div class="flex">
                                        <input @change="sumExpenses()" v-model="expense.insurance.value" type="number" id="insurance" placeholder="3,200" required class="input-rounded-l">
                                        <select @change="sumExpenses()" v-model="expense.insurance.period" class="select-rounded-r">
                                            <option :value="period.value"
                                                v-for="period in periods">
                                                {{ period.label }}
                                            </option>
                                        </select>
                                    </div>
                                </div>
                        </div>
                        <div>
                            <h3>Utilities</h3>
                            <div class="my-3">
                                <label for="electricity" class="block mb-2 text-sm font-medium text-gray-900 dark:text-white">Electricity {{ convertToAnnual(expense.electricity.value, expense.electricity.period) }}</label>
                                <div class="flex">
                                    <input @change="sumExpenses()" v-model="expense.electricity.value" type="number" id="electricity" placeholder="100,000" required class="input-rounded-l">
                                    <select @change="sumExpenses()" v-model="expense.electricity.period" class="select-rounded-r">
                                        <option :value="period.value" 
                                            v-for="period in periods">
                                            {{ period.label }}
                                        </option>
                                    </select>
                                </div>
                            </div>
                            <div class="my-3">
                                <div class="my-3">
                                    <label for="gas" class="block mb-2 text-sm font-medium text-gray-900 dark:text-white">Gas {{ convertToAnnual(expense.gas.value, expense.gas.period) }}</label>
                                    <div class="flex">
                                        <input @change="sumExpenses()" v-model="expense.gas.value" type="number" id="gas" placeholder="200" required class="input-rounded-l">
                                        <select @change="sumExpenses()" v-model="expense.gas.period" class="select-rounded-r">
                                            <option :value="period.value"
                                                v-for="period in periods">
                                                {{ period.label }}
                                            </option>
                                        </select>
                                    </div>
                                </div>
                                <div class="my-3">
                                    <label for="water" class="block mb-2 text-sm font-medium text-gray-900 dark:text-white">Water {{ convertToAnnual(expense.water.value, expense.water.period) }}</label>
                                    <div class="flex">
                                        <input @change="sumExpenses()" v-model="expense.water.value" type="number" id="rates" placeholder="200" required class="input-rounded-l">
                                        <select @change="sumExpenses()" v-model="expense.water.period" class="select-rounded-r">
                                            <option :value="period.value"
                                                v-for="period in periods">
                                                {{ period.label }}
                                            </option>
                                        </select>
                                    </div>
                                </div>
                                <div class="my-3">
                                    <label for="gardening" class="block mb-2 text-sm font-medium text-gray-900 dark:text-white">Gardening {{ convertToAnnual(expense.gardening.value, expense.gardening.period) }}</label>
                                    <div class="flex">
                                        <input @change="sumExpenses()" v-model="expense.gardening.value" type="number" id="gardening" placeholder="3,200" required class="input-rounded-l">
                                        <select @change="sumExpenses()" v-model="expense.gardening.period" class="select-rounded-r">
                                            <option :value="period.value"
                                                v-for="period in periods">
                                                {{ period.label }}
                                            </option>
                                        </select>
                                    </div>
                                </div>
                                <div class="my-3">
                                    <label for="pool" class="block mb-2 text-sm font-medium text-gray-900 dark:text-white">Pool {{ convertToAnnual(expense.pool.value, expense.pool.period) }}</label>
                                    <div class="flex">
                                        <input @change="sumExpenses()" v-model="expense.pool.value" type="number" id="pool" placeholder="3,200" required class="input-rounded-l">
                                        <select @change="sumExpenses()" v-model="expense.pool.period" class="select-rounded-r">
                                            <option :value="period.value"
                                                v-for="period in periods">
                                                {{ period.label }}
                                            </option>
                                        </select>
                                    </div>
                                </div>
                            </div>
                        </div>
                        <div>
                            <h3>Comms & Tech</h3>
                            <div class="my-3">
                                <label for="landline" class="block mb-2 text-sm font-medium text-gray-900 dark:text-white">Landline {{ convertToAnnual(expense.landline.value, expense.landline.period) }}</label>
                                <div class="flex">
                                    <input @change="sumExpenses()" v-model="expense.landline.value" type="number" id="landline" placeholder="100,000" required class="input-rounded-l">
                                    <select @change="sumExpenses()" v-model="expense.landline.period" class="select-rounded-r">
                                        <option :value="period.value" 
                                            v-for="period in periods">
                                            {{ period.label }}
                                        </option>
                                    </select>
                                </div>
                            </div>
                            <div class="my-3">
                                <div class="my-3">
                                    <label for="mobile" class="block mb-2 text-sm font-medium text-gray-900 dark:text-white">Mobile {{ convertToAnnual(expense.mobile.value, expense.mobile.period) }}</label>
                                    <div class="flex">
                                        <input @change="sumExpenses()" v-model="expense.mobile.value" type="number" id="mobile" placeholder="200" required class="input-rounded-l">
                                        <select @change="sumExpenses()" v-model="expense.mobile.period" class="select-rounded-r">
                                            <option :value="period.value"
                                                v-for="period in periods">
                                                {{ period.label }}
                                            </option>
                                        </select>
                                    </div>
                                </div>
                                <div class="my-3">
                                    <label for="internet" class="block mb-2 text-sm font-medium text-gray-900 dark:text-white">Internet {{ convertToAnnual(expense.internet.value, expense.internet.period) }}</label>
                                    <div class="flex">
                                        <input @change="sumExpenses()" v-model="expense.internet.value" type="number" id="internet" placeholder="200" required class="input-rounded-l">
                                        <select @change="sumExpenses()" v-model="expense.internet.period" class="select-rounded-r">
                                            <option :value="period.value"
                                                v-for="period in periods">
                                                {{ period.label }}
                                            </option>
                                        </select>
                                    </div>
                                </div>
                                <div class="my-3">
                                    <label for="security" class="block mb-2 text-sm font-medium text-gray-900 dark:text-white">Security {{ convertToAnnual(expense.security.value, expense.security.period) }}</label>
                                    <div class="flex">
                                        <input @change="sumExpenses()" v-model="expense.security.value" type="number" id="security" placeholder="3,200" required class="input-rounded-l">
                                        <select @change="sumExpenses()" v-model="expense.security.period" class="select-rounded-r">
                                            <option :value="period.value"
                                                v-for="period in periods">
                                                {{ period.label }}
                                            </option>
                                        </select>
                                    </div>
                                </div>
                            </div>
                        </div>
                        <div>
                            <h3>Vehicle</h3>
                            <div class="my-3">
                                <label for="repayments" class="block mb-2 text-sm font-medium text-gray-900 dark:text-white">Repayments {{ convertToAnnual(expense.vehicle.repayments.value, expense.vehicle.repayments.period) }}</label>
                                <div class="flex">
                                    <input @change="sumExpenses()" v-model="expense.vehicle.repayments.value" type="number" id="repayments" placeholder="100,000" required class="input-rounded-l">
                                    <select @change="sumExpenses()" v-model="expense.vehicle.repayments.period" class="select-rounded-r">
                                        <option :value="period.value" 
                                            v-for="period in periods">
                                            {{ period.label }}
                                        </option>
                                    </select>
                                </div>
                            </div>
                            <div class="my-3">
                                <div class="my-3">
                                    <label for="repair" class="block mb-2 text-sm font-medium text-gray-900 dark:text-white">Repair {{ convertToAnnual(expense.vehicle.repair.value, expense.vehicle.repair.period) }}</label>
                                    <div class="flex">
                                        <input @change="sumExpenses()" v-model="expense.vehicle.repair.value" type="number" id="repair" placeholder="200" required class="input-rounded-l">
                                        <select @change="sumExpenses()" v-model="expense.vehicle.repair.period" class="select-rounded-r">
                                            <option :value="period.value"
                                                v-for="period in periods">
                                                {{ period.label }}
                                            </option>
                                        </select>
                                    </div>
                                </div>
                                <div class="my-3">
                                    <label for="registration" class="block mb-2 text-sm font-medium text-gray-900 dark:text-white">Registration {{ convertToAnnual(expense.vehicle.registration.value, expense.vehicle.registration.period) }}</label>
                                    <div class="flex">
                                        <input @change="sumExpenses()" v-model="expense.vehicle.registration.value" type="number" id="registration" placeholder="200" required class="input-rounded-l">
                                        <select @change="sumExpenses()" v-model="expense.vehicle.registration.period" class="select-rounded-r">
                                            <option :value="period.value"
                                                v-for="period in periods">
                                                {{ period.label }}
                                            </option>
                                        </select>
                                    </div>
                                </div>
                                <div class="my-3">
                                    <label for="insurance" class="block mb-2 text-sm font-medium text-gray-900 dark:text-white">Insurance {{ convertToAnnual(expense.vehicle.insurance.value, expense.vehicle.insurance.period) }}</label>
                                    <div class="flex">
                                        <input @change="sumExpenses()" v-model="expense.vehicle.insurance.value" type="number" id="insurance" placeholder="3,200" required class="input-rounded-l">
                                        <select @change="sumExpenses()" v-model="expense.vehicle.insurance.period" class="select-rounded-r">
                                            <option :value="period.value"
                                                v-for="period in periods">
                                                {{ period.label }}
                                            </option>
                                        </select>
                                    </div>
                                </div>
                                <div class="my-3">
                                    <label for="fuel" class="block mb-2 text-sm font-medium text-gray-900 dark:text-white">Fuel {{ convertToAnnual(expense.vehicle.fuel.value, expense.vehicle.fuel.period) }}</label>
                                    <div class="flex">
                                        <input @change="sumExpenses()" v-model="expense.vehicle.fuel.value" type="number" id="fuel" placeholder="3,200" required class="input-rounded-l">
                                        <select @change="sumExpenses()" v-model="expense.vehicle.fuel.period" class="select-rounded-r">
                                            <option :value="period.value"
                                                v-for="period in periods">
                                                {{ period.label }}
                                            </option>
                                        </select>
                                    </div>
                                </div>
                            </div>
                        </div>
                        <div>
                            <h3>Health & Finance</h3>
                            <div class="my-3">
                                <label for="medical" class="block mb-2 text-sm font-medium text-gray-900 dark:text-white">Medical {{ convertToAnnual(expense.medical.value, expense.medical.period) }}</label>
                                <div class="flex">
                                    <input @change="sumExpenses()" v-model="expense.medical.value" type="number" id="medical" placeholder="100,000" required class="input-rounded-l">
                                    <select @change="sumExpenses()" v-model="expense.medical.period" class="select-rounded-r">
                                        <option :value="period.value" 
                                            v-for="period in periods">
                                            {{ period.label }}
                                        </option>
                                    </select>
                                </div>
                            </div>
                            <div class="my-3">
                                <div class="my-3">
                                    <label for="gym" class="block mb-2 text-sm font-medium text-gray-900 dark:text-white">Gym & sports {{ convertToAnnual(expense.gym.value, expense.gym.period) }}</label>
                                    <div class="flex">
                                        <input @change="sumExpenses()" v-model="expense.gym.value" type="number" id="gym" placeholder="200" required class="input-rounded-l">
                                        <select @change="sumExpenses()" v-model="expense.gym.period" class="select-rounded-r">
                                            <option :value="period.value"
                                                v-for="period in periods">
                                                {{ period.label }}
                                            </option>
                                        </select>
                                    </div>
                                </div>
                                <div class="my-3">
                                    <label for="laundry" class="block mb-2 text-sm font-medium text-gray-900 dark:text-white">Laundry {{ convertToAnnual(expense.laundry.value, expense.laundry.period) }}</label>
                                    <div class="flex">
                                        <input @change="sumExpenses()" v-model="expense.laundry.value" type="number" id="laundry" placeholder="200" required class="input-rounded-l">
                                        <select @change="sumExpenses()" v-model="expense.laundry.period" class="select-rounded-r">
                                            <option :value="period.value"
                                                v-for="period in periods">
                                                {{ period.label }}
                                            </option>
                                        </select>
                                    </div>
                                </div>
                                <div class="my-3">
                                    <label for="credit" class="block mb-2 text-sm font-medium text-gray-900 dark:text-white">Credit repayments {{ convertToAnnual(expense.credit.value, expense.credit.period) }}</label>
                                    <div class="flex">
                                        <input @change="sumExpenses()" v-model="expense.credit.value" type="number" id="credit" placeholder="3,200" required class="input-rounded-l">
                                        <select @change="sumExpenses()" v-model="expense.credit.period" class="select-rounded-r">
                                            <option :value="period.value"
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
                                <label for="income" class="block mb-2 text-sm font-medium text-gray-900 dark:text-white">Income {{ convertToAnnual(asset.income.value, asset.income.period) }}</label>
                                <div class="flex">
                                    <input @change="sumIncome()" v-model="asset.income.value" type="number" name="rental_income" id="rental_income" class="input-rounded-l" required>
                                    <select @change="sumIncome()" v-model="asset.income.period" class="select-rounded-r">
                                        <option :value="period.value"
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
                                <label for="income" class="block mb-2 text-sm font-medium text-gray-900 dark:text-white">Income {{convertToAnnual(asset.income.value, asset.income.period) }}</label>
                                <div class="flex">
                                    <input @change="sumIncome()" v-model="asset.income.value" type="number" name="rental_income" id="rental_income" class="input-rounded-l" required>
                                    <select @change="sumIncome()" v-model="asset.income.period" class="select-rounded-r">
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
                                <label for="income" class="block mb-2 text-sm font-medium text-gray-900 dark:text-white">Income {{convertToAnnual(asset.income.value, asset.income.period) }}</label>
                                <div class="flex">
                                    <input @change="sumIncome()" v-model="asset.income.value" type="number" name="rental_income" id="rental_income" class="input-rounded-l" required>
                                    <select @change="sumIncome()" v-model="asset.income.period" class="select-rounded-r">
                                        <option 
                                            v-for="period in periods">
                                            {{ period.label }}
                                        </option>
                                    </select>
                                </div>
                            </div>
                            <div>
                                <label for="expense" class="block mb-2 text-sm font-medium text-gray-900 dark:text-white">Expense</label>
                                <input v-model="asset.expense.value" type="number" name="expense" id="expense" class="bg-gray-50 border border-gray-300 text-gray-900 text-sm rounded-lg focus:ring-blue-500 focus:border-blue-500 block w-full p-2.5 dark:bg-gray-600 dark:border-gray-500 dark:placeholder-gray-400 dark:text-white" required>
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
                                <label for="rental_income" class="block mb-2 text-sm font-medium text-gray-900 dark:text-white">Income {{ convertToAnnual(asset.income.value, asset.income.period) }}</label>
                                <div class="flex">
                                    <input @change="sumIncome()" v-model="asset.income.value" type="number" name="rental_income" id="rental_income" class="input-rounded-l" required>
                                    <select @change="sumIncome()" v-model="asset.income.period" class="select-rounded-r">
                                        <option :value="period.value" 
                                            v-for="period in periods">
                                            {{ period.label }}
                                        </option>
                                    </select>
                                </div>
                            </div>
                            <div>
                                <label for="expense" class="block mb-2 text-sm font-medium text-gray-900 dark:text-white">Expense</label>
                                <input v-model="asset.expense.value" type="number" name="expense" id="expense" class="bg-gray-50 border border-gray-300 text-gray-900 text-sm rounded-lg focus:ring-blue-500 focus:border-blue-500 block w-full p-2.5 dark:bg-gray-600 dark:border-gray-500 dark:placeholder-gray-400 dark:text-white" required>
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
                                    <input @change="sumIncome()" v-model="asset.income.value" type="number" name="rental_income" id="rental_income" class="input-rounded-l" required>
                                    <select @change="sumIncome()" v-model="asset.income.period" class="select-rounded-r">
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
                                    <input @change="sumIncome()" v-model="asset.expense.value" type="number" name="expense" id="expense" class="input-rounded-l" required>
                                    <select @change="sumIncome()" v-model="asset.expense.period" class="select-rounded-r">
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
                    salaryTotal: {{ salaryTotal }}
                </p>
                <p>
                    incomeTotal: {{ incomeTotal}}
                </p>
                <p>
                    expenseTotal: {{ expenseTotal}}
                </p>
                <p>
                    Expense: {{ expense }}
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
import Currency from '../components/dashboard/cards/currency.vue';


let showIndividualForm = ref(false)
let entities = ref([]);
let assets = ref([]);
let selectedAsset = {};  
let showDebug = true;
let incomeTotal = ref(0)
let salaryTotal = ref(0)
let assetIncomeTotal = ref(0)
let expenseTotal = ref(0)
let cashflowTotal = ref(0)

let individualFormData = {
    type: "individual", 
    label: "Individual",
    first_name: "",
    last_name: "",
    phone_number: "",
    email: "",
    compliance: false,
    salary: {
        value: 0,
        period: 'yearly'
    }
}

let expense = ref({
    residence: {type: "", label: "Primary residence", value: ""},
    rent: {label: "Rent", value: 700, period: "weekly"},
    mortgage: {label: "Mortgage", value: 2800, period: "monthly"},
    bodycorporate: {label: "Body corporate", value: 1200, period: "quarterly"},
    rates: {label: "Rates", value: 1200, period: "quarterly"},
    electricity: {label: "Electricity", value: 0, period: "quarterly"},
    gas: {label: "Gas", value: 0, period: "quarterly"},
    water: {label: "Water", value: 0, period: "quarterly"},
    mobile: {label: "Mobile", value: 0, period: "monthly"},
    landline: {label: "Landline", value: 0, period: "monthly"},
    internet: {label: "Internet", value: 0, period: "monthly"},
    insurance: {label: "Home & contens insurance", value: 0, period: "yearly"},
    security: {label: "Security", value: 0, period: "monthly"},
    gardening: {label: "Gardening", value: 0, period: "monthly"},
    cleaner: {label: "Cleaner", value: 0, period: "monthly"},
    pool: {label: "Pool maintenance", value: 0, period: "yearly"},
    credit: {label: "Credit repayments", value: 0, period: "monthly"},
    laundry: {label: "Laundry", value: 0, period: "monthly"},
    medical: {label: "Medical", value: 0, period: "monthly"},
    gym: {label: "Gym / sports", value: 0, period: "monthly"},
    vehicle: {
        repayments: {label: "Vehicle repayments", value: 0, period: "monthly"},
        repair: {label: "Vehicle repair", value: 0, period: "monthly"},
        registration: {label: "Vehicle registration", value: 0, period: "monthly"},
        insurance: {label: "Vehicle insurance", value: 0, period: "yearly"},
        fuel: {label: "Fuel", value: 0, period: "weekly"},
    }
})

const periods = [
    {value: "weekly", label: "Weekly"},
    {value: "fortnightly", label: "Fortnightly"},
    {value: "monthly", label: "Monthly"},
    {value: "quarterly", label: "Quarterly"},
    {value: "yearly", label: "Yearly"},
]

const sumExpenses = () => {
    let sum = 0;
    for (let key in expense.value) {
        if (expense.value[key].hasOwnProperty('value')) {
            switch (expense.value[key].period) {
                case "weekly":
                    sum += expense.value[key].value * 52;
                    break;
                case "monthly":
                    sum += expense.value[key].value * 12;
                    break;
                case "quarterly":
                    sum += expense.value[key].value * 4;
                    break;
                case "yearly":
                    sum += expense.value[key].value;
                    break;
            }
        } else {
            for (let subKey in expense.value[key]) {
                if (expense.value[key][subKey].hasOwnProperty('value')) {
                    switch (expense.value[key][subKey].period) {
                        case "weekly":
                            sum += expense.value[key][subKey].value * 52;
                            break;
                        case "monthly":
                            sum += expense.value[key][subKey].value * 12;
                            break;
                        case "quarterly":
                            sum += expense.value[key][subKey].value * 4;
                            break;
                        case "yearly":
                            sum += expense.value[key][subKey].value;
                            break; 
                    }
                }
            }
        }
    }
    expenseTotal.value = sum
}

const toggleIndividualForm = () => {
    showIndividualForm.value = !showIndividualForm.value
}

const addToEntities = () => {
    // clone individualFormData and push it to entities
    entities.value.push({...individualFormData})
    closeIndividualForm()
}

const sumSalary = () => {
    salaryTotal.value = 0
    // Sum all salaries
    entities.value.forEach(entity => {
        let multiplier = 0;
        switch (entity.salary.period) {
            case "weekly":
                multiplier = 52;
                break;
            case "fortnightly":
                multiplier = 26;
                break;
            case "monthly":
                multiplier = 12;
                break;
            case "yearly":
                multiplier = 1;
                break; 
        }
        salaryTotal.value += entity.salary.value * multiplier;
    });
}

const sumAssetIncome = () => {
    assetIncomeTotal.value = 0
    // Sum all asset income
    assets.value.forEach(asset => {
        let multiplier = 0;
        switch (asset.income.period) {
            case "weekly":
                multiplier = 52;
                break;
            case "fortnightly":
                multiplier = 26;
                break;
            case "monthly":
                multiplier = 12;
                break;
            case "yearly":
                multiplier = 1;
                break; 
        }
        assetIncomeTotal.value += asset.income.value * multiplier;
    });
}

const sumIncome = () => {
    sumSalary()
    sumAssetIncome()
    incomeTotal.value = salaryTotal.value + assetIncomeTotal.value
    cashflowTotal.value = incomeTotal.value - expenseTotal.value
    console.log(incomeTotal.value, expenseTotal.value)
}

const convertToAnnual = (value: Number,period: String) => {
    let multiplier = 0

    switch (period) {
        case "weekly":
            multiplier = 52
            break;
    
        case "fortnightly":
            multiplier = 26 
            break;
    
        case "monthly":
            multiplier = 12 
            break;
        
        case "quarterly":
            multiplier = 4 
            break;
        
        case "yearly":
            multiplier = 1
    }

    if(value > 0 && period){
        value = value * multiplier 
        return convertToDollars(value) + "/pa"
    }
}

const yearlySalary = (entity) => {
    let multiplier = 0
    switch (entity.salary.period) {
        case "weekly":
            multiplier = 52
            break;
    
        case "fortnightly":
            multiplier = 26 
            break;
    
        case "monthly":
            multiplier = 12 
            break;
        
        case "yearly":
            multiplier = 1
    }

    if(entity.salary.value > 0 && entity.salary.period){
        
        let salary = entity.salary.value * multiplier 
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
        return convertToDollars(income) + "/pa"
        // return income.toString().replace(/\D/g, "").replace(/\B(?=(\d{3})+(?!\d))/g, ",") + "/pa";
    }
}

const convertToDollars = (value) => {
    return value.toString().replace(/\D/g, "").replace(/\B(?=(\d{3})+(?!\d))/g, ",");
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
  {type: "savings", label: "Savings", interest_rate: 0.5, total_value: 2000, income:{value: 100, period: "yearly"}},
  {type: "stocks", label: "Stocks", dividend_rate: 4.5, total_value: 5000, income: 225, income_period: "yearly"},
  {type: "real_estate_commercial", label: "Commercial Real Estate", income: {value: 9000, period: "monthly"}, expense: {value:1000, period: "monthly"}, total_value: 1000000},
  {type: "real_estate_residential", label: "Residential Real Estate", income: {value: 666, period: "weekly"}, expense: {value: 1000,period: "monthly"}, total_value: 1000000},
  {type: "collectibles", label: "Collectibles", total_value: 1000},
  {type: "eft", label: "EFT", dividend_rate: 4.5, total_value: 5000, income: {value: 225, period: "weekly"}},
  {type: "bonds", label: "Bonds", dividend_rate: 4.1, total_value: 10000, income: {value: 410, period: "weekly"}},
  {type: "mutual_funds", label: "Mutual Funds", dividend_rate: 4.5, total_value: 5000, income:{value: 225, period: "weekly"}},
  {type: "term_deposits", label: "Term Deposits", dividend_rate: 5.2, total_value: 50000, income: {value: 2600, period: "weekly"}},
  {type: "business", label: "Business", income: {value: 6000, period: "monthly"}, expense: {value: 3000, pense_period: "monthly"}, total_value: 50000},
]



</script>

<style scoped>
</style>