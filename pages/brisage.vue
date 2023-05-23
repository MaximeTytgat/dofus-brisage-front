<script lang="ts" setup>
    import { reactive, toRefs } from 'vue';
    
    interface Ressources {
        [champ: string]: {count: number, price: number};
    }

    interface State {
        text: string;
        item_name: string;
        total_price: string;
        ressources: Ressources;
        foo: number;
    }

    const state: State = reactive({
        text: '',
        item_name: '',
        total_price: '',
        ressources: {},
        foo: 0
    });

    const parse = () => {
        // get Total price: Use regex to get all number before 'kamas' and sum all
        const prices: number[] | undefined = [];

        // get Ressources Count and Prices
        const price_regex: RegExp = /(\d+) x \[(.*?)\] \(([\d\s]+) kamas\)/g;
        let match: RegExpMatchArray | null;
        while ((match = price_regex.exec(state.text)) !== null) {
            if (state.ressources[match[2]]) {
                state.ressources[match[2]].count += parseInt(match[1]);
            } else {
                state.ressources[match[2]] = { count: parseInt(match[1]), price: parseInt(match[1]) > 1 ? Math.round(parseInt(match[3].replace(/\s/g, '')) / parseInt(match[1])) : parseInt(match[3].replace(/\s/g, '')) };
            }
            prices?.push(parseInt(match[3].replace(/\s/g, '')));
        }

        const prices_sum: number | undefined = Object.values(state.ressources).reduce((total, ressource) => total + ressource.price * ressource.count, 0);
        state.total_price = prices_sum ? `${prices_sum} kamas` : 'Aucun prix trouvé';

        // get Item name: Use regex to get all text between 'Vous avez créé' and '!'
        const item_regex: RegExp = /(?<=Vous avez créé \d+ × \[).+(?=\] !)/g;
        const finded_name: RegExpMatchArray | null = state.text.match(item_regex);
        state.item_name = finded_name ? finded_name[0] : 'Aucun item trouvé';

        // console.log(state.item_name);
        console.log(state.ressources);
        // console.log(state.total_price);
    }

    const reset = (): void => {
        state.text = '';
        state.item_name = '';
        state.total_price = '';
        state.ressources = {};
        state.foo = 0;
    }
    
    const increment = (name: string | number): void => {
        state.ressources[name].count++;
    }
    const decrement = (name: string | number): void => {
        state.ressources[name].count--;
    }

    const refs = toRefs(state); // Convert reactive state to refs
</script>

<template>
    <div class="d-flex brisageContainer h-100">
        <div class="w-33 pa-4">
            <h2 class="mb-2 text-center">Dofus Chat</h2>
            <div class="d-flex flex-column">
                <textarea v-model="refs.text.value" class="mb-4 parser card" wrap="off" name="parser" id="parser"></textarea>
                <div class="d-flex justify-space-around">
                    <v-btn color="#fa4545" @click="reset" icon="mdi-reload"></v-btn>
                    <v-btn color="#02B96E" @click="parse" icon="mdi-check-bold"></v-btn>
                </div>
            </div>
        </div>
        <div class="w-66 pa-4">
            <h2 class="mb-2 text-center">Brisage</h2>
            <div class="d-flex flex-column">
                <v-card class="card h-100 pa-4">
                    <div class="d-flex mb-4">
                        <div class="ml-2 w-50 text-h5">Item: <span class="font-weight-bold">{{ refs.item_name.value }}</span></div>
                        <div class="ml-2 w-50 text-h5">Prix total: <span class="font-weight-bold">{{ refs.total_price.value }}</span></div>
                    </div>
            
                    <div class="d-flex mb-4">
                        <v-text-field class="ma-2" variant="outlined" label="Brisage %" hide-details="auto" type="number"></v-text-field>
                        <v-text-field class="ma-2" variant="outlined" label="Resultat" hide-details="auto" type="number"></v-text-field>
                    </div>

                    <div class="d-flex mb-4">
                        <div class="ml-2 w-50 text-h5">Craft:</div>
                    </div>

                    <div class="d-flex flex-wrap">
                        <div v-for="(number, name) in refs.ressources.value" class="card pa-4 mr-4 mb-4" style="width: 200px;">
                            <div class="mb-4">
                                {{ name }}
                            </div>
                            <v-text-field class="" style="width: 150px;" variant="outlined" v-model="refs.ressources.value[name].count" type="number" label="Number" append-icon="mdi-plus" @click:append="increment(name)" prepend-icon="mdi-minus" @click:prepend="decrement(name)"></v-text-field>
                            <div>
                                coup: {{ refs.ressources.value[name].price * refs.ressources.value[name].count }} kamas
                            </div>
                        </div>
                    </div>
                </v-card>
            </div>
        </div>
    </div>
</template>
<style lang="scss" scoped>
    .brisageContainer {
        background-color: #292929;
        color: #02B96E;
    }

    .card {
        background-color: #181818;
        border: solid #02B96E 1px;
        color: #00b991;
        border-radius: 20px;
    }

    .parser {
        resize: none;
        font-weight: 600;
        padding: 20px;
        overflow-y: auto;
        overflow-x: hidden;
        min-height: 400px;
        max-height: 500px;

        &:focus {
            outline: none;
            border-color: #05ff97;
        }
        &::-webkit-scrollbar {
            width: 0px;
            background: transparent;
        }
    }
</style>