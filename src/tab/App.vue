<template>
    <div wrap-card-page> 
        <div loading-bar :complete="loadingDone"></div>
        <div header-page>
            <div item>
                <a href="https://magic.wizards.com/" class="logo">
                    <img src="../images/MX_Nav_EN.png" alt="MTG">
                </a>
            </div>
        </div>
        <div bg-card-page>
            <span v-if="card.card_faces" :style="{'background-image':'url('+ card.card_faces[0].image_uris.art_crop +')'}" :active="loadingDone"></span>
            <span v-if="!card.card_faces" :style="{'background-image':'url('+ card.image_uris.art_crop +')'}" :active="loadingDone"></span>
        </div>
        <div wrap-content-card>
            <div card-preview :active="loadingDone">
                <div class="flip-box" :fliped="flipCard" v-if="!loading && card.card_faces">
                    <div class="rotate-btn" @click="flipAction"></div>
                    <div class="flip-box-inner">
                        <div class="flip-box-front">
                            <img :src="card.card_faces[0].image_uris['large']" :title="card.card_faces[0].name" />
                        </div>
                        <div class="flip-box-back">
                            <img :src="card.card_faces[1].image_uris['large']" :title="card.card_faces[1].name" />
                        </div>
                    </div>
                </div>
                <!-- Single Card -->
                <div class="img-card" v-if="!loading && !card.card_faces">
                    <img :src="card.image_uris['large']" :alt="card.name" :name="card.name">
                </div>
            </div>
            <div card-info>
                <div wrap-infos>
                    <div header-infos>
                        <h2 title-card>{{ card.name }}</h2>
                        <div mana-cout>
                            <div v-if="!card.card_faces && manaCost">
                                <i 
                                    v-for="(mana, index) in manaCost" 
                                    :key="'mana-' + index" 
                                    :class="'card-symbol card-symbol-' + mana"
                                >
                                    {{ mana }}
                                </i>
                            </div>
                            <div v-if="card.card_faces && manaCostArray[0]">
                                <i 
                                    v-for="(mana, index) in manaCostArray[0]" 
                                    :key="'mana-' + index" 
                                    :class="'card-symbol card-symbol-' + mana"
                                >
                                    {{ mana }}
                                </i>
                            </div>
                        </div>
                    </div>
                    <div description-info>
                        <div v-if="card.card_faces">
                            <h5>{{ card.card_faces[0].type_line }}</h5>
                            <p v-html="$options.filters.textConvert(card.card_faces[0].oracle_text)"></p>
                            <h5>{{ card.card_faces[1].type_line }}</h5>
                            <p v-html="$options.filters.textConvert(card.card_faces[1].oracle_text)"></p>
                        </div>
                        <div v-if="!card.card_faces">
                            <h5>{{ card.type_line }}</h5>
                            <p v-html="$options.filters.textConvert(card.oracle_text)"></p>
                        </div>
                    </div>
                </div>
            </div>
        </div>

        <div footer-page>
            <address>
                <i>© 1993-{{ year }} Wizards of the Coast LLC, a subsidiary of Hasbro, Inc. All Rights Reserved.</i>
            </address>
            <span>Extenssion developed by <a href="https://twitter.com/papaulov">@papaulov</a>. API by <a href="https://scryfall.com/">Scryfall</a></span>
        </div>
    </div>
</template>

<script>
import axios from 'axios';

export default {
    components:{
        //
    },
    data () {
        return {
            message: "My new tab page",
            card: [],
            loading: true,
            loadingDone: false,
            flipCard: false,
            year: null,
            manaCost: null,
            manaCostArray: [],
        }
    },
    mounted() {
        const today = new Date();
        const year = today.getFullYear();
        this.year = year;
        this.selectCard();
    },
    filters: {
        manaConvert(value) {
            return value.replace(/[\])}[{(]/g, '');
        },
        textConvert(string){
            return string.replace(/\{(.*?)\}/gm, "<span class='card-symbol card-symbol-$1'>$1</span>").replace(/\.\s/g, '.<br>');
        }
    },
    methods: {
        flipAction() {
            this.flipCard = !this.flipCard;
        },
        selectCard() {
            const that = this;
            this.loading = true;
            const url = 'https://api.scryfall.com/cards/named?fuzzy=chandras-pyreling';
            const urlDuo = 'https://api.scryfall.com/cards/named?fuzzy=nicol-bolas-the-ravager-nicol-bolas-the-arisen';
            const urlRandom = 'https://api.scryfall.com/cards/random';

            axios.get(`${url}`).then((response) => {
               that.card = response.data;
               that.loading = false;

                if (response.data.card_faces) {
                    response.data.card_faces.filter(cur => {
                        that.manaCostArray.push(this.$options.filters.manaConvert(cur.mana_cost))
                    })
                } else {
                    that.manaCost = this.$options.filters.manaConvert(response.data.mana_cost);
                }

               setTimeout(() => {
                   that.loadingDone = true;
               }, 1000)
            }).catch(function (error) {
                console.log(error);
            });
        }
    }
}
</script>

<style scoped>
    @import 'tab.css';
</style>