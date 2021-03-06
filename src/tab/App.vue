<template>
    <div wrap-card-page> 
        <div loading-bar :complete="loadingDone"></div>
        <div header-page>
            <div item>
                <span class="logo">
                    <img src="/images/mtg_random_cards.svg" alt="MTG">
                </span>
            </div>
            <div items-rarity>
                <div item>
                    <span dot :active="is_common"></span>
                    <span count>{{ common }}</span>
                    <span>Common</span>
                </div>
                <div item>
                    <span dot :active="is_uncommon"></span>
                    <span count>{{ uncommon }}</span>
                    <span>Uncommon</span>
                </div>
                <div item>
                    <span dot :active="is_rare"></span>
                    <span count>{{ rare }}</span>
                    <span>Rare</span>
                </div>
                <div item>
                    <span dot :active="is_mythic"></span>
                    <span count>{{ mythic }}</span>
                    <span>Mythic</span>
                </div>
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
                            <span class="card-foil" v-if="card.foil"></span>
                            <img :src="card.card_faces[0].image_uris['large']" :title="card.card_faces[0].name" />
                        </div>
                        <div class="flip-box-back">
                            <img :src="card.card_faces[1].image_uris['large']" :title="card.card_faces[1].name" />
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
                                    <i>{{ cardFace.flavor_text }}</i>
                                </span>
                            </div>
                        </div>
                    </div>
                    <div v-else>
                        <div header-infos>
                            <h2 title-card :class="{'middle': card.name.length >= 20}">{{ card.name }}</h2>
                            <div mana-cout>
                                <i v-html="manaCost"></i>
                            </div>
                        </div>
                        <div description-info>
                            <div>
                                <h5>{{ card.type_line }}</h5>
                                <article v-html="$options.filters.textConvert(card.oracle_text)"></article>
                                <span flavor-text v-if="card.flavor_text">
                                    <i>{{ card.flavor_text.replace(' —',' —\n').replace(' — ',' —\n') }}</i>
                                </span>
                            </div>
                        </div>
                    </div>
                    <div legalities>
                        <h4>Legalities in:</h4>
                        <div col>
                            <div v-for="(leg, l) in card.legalities" :key="'leg' + l">
                                <div item v-if="leg == 'legal'">
                                    <span :class="leg">{{ l }}</span>
                                </div>
                            </div>
                        </div>
                        <div col>
                            <div v-for="(leg, l) in card.legalities" :key="'no_leg' + l">
                                <div item v-if="leg == 'not_legal'">
                                    <span :class="leg">{{ l }}</span>
                                </div>
                            </div>
                        </div>
                    </div>
                    <div release><span>Released in {{ card.released_at.replace('-','/').replace('-','/') }}</span></div>
                </div>
            </div>
        </div>

        <div footer-page>
            <address>
                <i>© 1993-{{ year }} <a href="https://company.wizards.com/">Wizards of the Coast LLC</a>, a subsidiary of <a href="https://products.hasbro.com/">Hasbro</a>, Inc. All Rights Reserved.</i>
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
            colorConcat: [],

            common: 0,
            is_common: false,
            uncommon: 0,
            is_uncommon: false,
            rare: 0,
            is_rare: false,
            mythic: 0,
            is_mythic: false,
        }
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
    mounted() {
        const today = new Date();
        const year = today.getFullYear();
        this.year = year;
        this.selectCard();
        this.createStorage();
        this.setStorageData();
    },
    watch: {
        common(value) {
            localStorage.common = value;
        },
        uncommon(value) {
            localStorage.uncommon = value;
        },
        rare(value) {
            localStorage.rare = value;
        },
        mythic(value) {
            localStorage.mythic = value;
        }
    },
    methods: {
        flipAction() {
            this.flipCard = !this.flipCard;
        },
        selectCard() {
            const that = this;
            this.loading = true;
            const cabuloso = 'https://api.scryfall.com/cards/named?fuzzy=bfm-(big-furry-monster)';
            const url = 'https://api.scryfall.com/cards/named?fuzzy=island';
            const urlDuo = 'https://api.scryfall.com/cards/named?fuzzy=nicol-bolas-the-ravager-nicol-bolas-the-arisen';
            const urlRandom = 'https://api.scryfall.com/cards/random';

            axios.get(`${urlRandom}`).then((response) => {
               that.card = response.data;
               that.loading = false;

            //    console.log(that.card)
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
                    // console.log(response.data.mana_cost)
                }

                if (response.data.set_uri) {
                    axios.get(`${response.data.set_uri}`).then((set) => {
                        that.cardSet = set.data;
                        // console.log(that.cardSet)
                    })
                }

                setTimeout(() => {
                    that.loadingDone = true;
                    this.plusRarity(that.card.rarity);
                }, 200)
            }).catch(function (error) {
                // console.log(error);
            });
        },
        createStorage() {
            // Lads
            if (!localStorage.common) localStorage.setItem('common', 0);
            if (!localStorage.uncommon) localStorage.setItem('uncommon', 0);
            if (!localStorage.rare) localStorage.setItem('rare', 0);
            if (!localStorage.mythic) localStorage.setItem('mythic', 0);
        },
        setStorageData() {
            if (localStorage.common) this.common = localStorage.common;
            if (localStorage.uncommon) this.uncommon = localStorage.uncommon;
            if (localStorage.rare) this.rare = localStorage.rare;
            if (localStorage.mythic) this.mythic = localStorage.mythic;
        },
        plusRarity(key) {
            if (key == 'common') {
                localStorage.common = Number(localStorage.common)+1;
                this.is_common = true;
                this.setStorageData();
            }
            if (key == 'uncommon') {
                localStorage.uncommon = Number(localStorage.uncommon)+1;
                this.is_uncommon = true;
                this.setStorageData();
            }
            if (key == 'rare') {
                localStorage.rare = Number(localStorage.rare)+1;
                this.is_rare= true;
                this.setStorageData();
            }
            if (key == 'mythic') {
                localStorage.mythic = Number(localStorage.mythic)+1;
                this.is_mythic = true;
                this.setStorageData();
            }
        }
    }
}
</script>

<style scoped>
    @import 'tab.css';
</style>