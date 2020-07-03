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
        <div bg-card-page v-if="!loading">
            <span v-if="card.card_faces && card.card_faces[0].image_uris" :style="{'background-image':'url('+ card.card_faces[0].image_uris.art_crop +')'}" :active="loadingDone"></span>
            <span v-else :style="{'background-image':'url('+ card.image_uris.art_crop +')'}" :active="loadingDone"></span>
        </div>
        <div wrap-content-card v-if="!loading">
            <div card-preview :active="loadingDone">
                <div class="flip-box" :fliped="flipCard" v-if="card.card_faces && card.card_faces[0].image_uris">
                    <div class="rotate-btn" @click="flipAction"></div>
                    <div class="flip-box-inner">
                        <div class="flip-box-front">
                            <img :src="card.card_faces[0].image_uris['large']" :title="card.card_faces[0].name" />
                            <span class="card-foil" v-if="card.foil"></span>
                        </div>
                        <div class="flip-box-back">
                            <img :src="card.card_faces[1].image_uris['large']" :title="card.card_faces[1].name" />
                            <span class="card-foil" v-if="card.foil"></span>
                        </div>
                    </div>
                    
                    <div artist>
                        <span>Artist: <a :href="'https://www.google.com/search?&tbm=isch&q=magic+artist+' + card.artist">{{ card.artist }}</a></span>
                    </div>
                </div>
                <!-- Single Card -->
                <div class="img-card" v-else>
                    <span class="card-foil" v-if="card.foil"></span>
                    <img :src="card.image_uris['large']" :alt="card.name" :name="card.name">

                    <div artist>
                        <span>Illustrated by <a :href="'https://www.google.com/search?&tbm=isch&q=magic+artist+' + card.artist">{{ card.artist }}</a></span>
                    </div>
                </div>
            </div>
            <div card-info :active="loadingDone">
                <div wrap-infos>
                    <div set-info>
                        <img :src="cardSet['icon_svg_uri']" :alt="cardSet.name" :name="cardSet.name" />
                        <span>{{ cardSet.name }}</span>
                    </div>
                    <div v-if="card.card_faces">
                        <div faces-card v-for="(cardFace, car) in card.card_faces" :key="'cardFace' + car">
                            <div header-infos>
                                <h2 title-card :class="{'middle': cardFace.name.length >= 20}">{{ cardFace.name }}</h2>
                                <div mana-cout>
                                    <div>
                                        <i 
                                            v-for="(mana, index) in cardFace.mana_cost" 
                                            :key="'mana-' + index" 
                                            :class="'card-symbol card-symbol-' + mana"
                                        >
                                            {{ mana }}
                                        </i>
                                    </div>
                                </div>
                            </div>
                            <div description-info>
                                <h5 v-if="cardFace.color_indicator">
                                    <i :class="'color-indicator color-indicator-' + colorConcat">{{ colorConcat }}</i>
                                    {{ cardFace.type_line }}
                                </h5>
                                <h5 v-else>{{ cardFace.type_line }}</h5>
                                <article v-html="$options.filters.textConvert(cardFace.oracle_text)"></article>
                                <span flavor-text v-if="cardFace.flavor_text">
                                    <i>{{ card.flavor_text }}</i>
                                </span>
                            </div>
                        </div>
                    </div>
                    <div v-else>
                        <div header-infos>
                            <h2 title-card>{{ card.name }}</h2>
                            <div mana-cout>
                                <i v-html="manaCost"></i>
                            </div>
                        </div>
                        <div description-info>
                            <div>
                                <h5>{{ card.type_line }}</h5>
                                <article v-html="$options.filters.textConvert(card.oracle_text)"></article>
                                <span flavor-text v-if="card.flavor_text">
                                    <i>{{ card.flavor_text }}</i>
                                </span>
                            </div>
                        </div>
                    </div>
                    <div release><span>Released in {{ card.released_at }}</span></div>
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
            cardSet: {},
            loading: true,
            loadingDone: false,
            flipCard: false,
            year: null,
            manaCost: null,
            manaCostArray: [],
            colorConcat: []
        }
    },
    mounted() {
        const today = new Date();
        const year = today.getFullYear();
        this.year = year;
        this.selectCard();
    },
    filters: {
        removeComma(value) {
            return value.replace(/,/g, "");
        },
        formatDate(value) {
            return value.replace('-','/');
        },
        manaConvert(value) {
            return value.replace(/\//,'').replace(/\//,'').replace(/\//,'').replace(/\//,'').replace(/\//,'').replace(/\{(.*?)\}/gm, '<span class="card-symbol card-symbol-$1">$1</span>');
        },
        textConvert(string){
            return string.replace(/\{(.*?)\}/gm, "<span class='card-symbol card-symbol-$1'>$1</span>").replace(/(.+?\n\n|.+?$)/gm, '<p>$1</p>').replace(/\((.*?)\)/gm, '<i>($1)</i>');
        }
    },
    methods: {
        flipAction() {
            this.flipCard = !this.flipCard;
        },
        selectCard() {
            const that = this;
            this.loading = true;
            const cabuloso = 'https://api.scryfall.com/cards/named?fuzzy=hit-run';
            const url = 'https://api.scryfall.com/cards/named?fuzzy=benalish-honor-guard';
            const urlDuo = 'https://api.scryfall.com/cards/named?fuzzy=nicol-bolas-the-ravager-nicol-bolas-the-arisen';
            const urlRandom = 'https://api.scryfall.com/cards/random';

            axios.get(`${urlRandom}`).then((response) => {
               that.card = response.data;
               that.loading = false;

               console.log(that.card)

                if (response.data.card_faces) {
                    response.data.card_faces.filter(cur => {
                        that.manaCostArray.push(this.$options.filters.manaConvert(cur.mana_cost))
                    })

                    if (response.data.card_faces[1].color_indicator) {
                        response.data.card_faces[1].color_indicator.filter(color => {
                            that.colorConcat.push(color);
                        })
                        that.colorConcat = that.colorConcat.join('');
                    }
                } else {
                    that.manaCost = this.$options.filters.manaConvert(response.data.mana_cost);
                    console.log(response.data.mana_cost)
                }

                if (response.data.set_uri) {
                    axios.get(`${response.data.set_uri}`).then((set) => {
                        that.cardSet = set.data;
                        console.log(that.cardSet)
                    })
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