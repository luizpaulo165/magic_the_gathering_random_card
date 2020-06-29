<template>
    <div wrap-card-page> 
        <div loading-bar :complete="loadingDone"></div>
        <div bg-card-page>
            <span v-if="card.card_faces" :style="{'background-image':'url('+ card.card_faces[0].image_uris.art_crop +')'}" :active="loadingDone"></span>
            <span v-if="!card.card_faces" :style="{'background-image':'url('+ card.image_uris.art_crop +')'}" :active="loadingDone"></span>
        </div>
        <div wrap-content-card>
            <div card-preview :active="loadingDone">
                <!-- <span img-fake></span> -->
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
                <figure v-if="!card.card_faces" :style="{'background-image':'url('+ card.image_uris.large +')'}"></figure>
            </div>
            <div card-info>
                <h2>card.name</h2>
                <p>Lorem ipsum dolor sit amet consectetur adipisicing elit. Nihil accusamus explicabo officia dolore dolorem at impedit recusandae corrupti consequuntur ut, consectetur vitae fuga natus suscipit commodi quibusdam necessitatibus eos adipisci.</p>
            </div>
        </div>
    </div>
</template>

<script>
import axios from 'axios';

export default {
    data () {
        return {
            message: "My new tab page",
            card: [],
            loading: true,
            loadingDone: false,
            flipCard: false
        }
    },
    mounted() {
        this.selectCard();
    },
    methods: {
        flipAction() {
            this.flipCard = !this.flipCard;
            console.log(0)
        },
        selectCard() {
            const that = this;
            this.loading = true;
            const url = 'https://api.scryfall.com/cards/named?fuzzy=trynn-champion-of-freedom';
            const urlDuo = 'https://api.scryfall.com/cards/named?fuzzy=nicol-bolas-the-ravager-nicol-bolas-the-arisen';
            const urlRandom = 'https://api.scryfall.com/cards/random';

            axios.get(`${urlRandom}`).then((response) => {
               that.card = response.data;
               that.loading = false;
               //
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