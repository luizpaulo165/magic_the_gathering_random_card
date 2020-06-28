<template>
    <div wrap-card-page> 
        <div bg-card-page>
            <span :style="{'background-image':'url('+ card.image_uris.art_crop +')'}"></span>
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
            loading: true
        }
    },
    mounted() {
        this.selectCard();
    },
    methods: {
        selectCard() {
            const that = this;
            this.loading = true;
            const url = 'https://api.scryfall.com/cards/named?fuzzy=chandra-novice-pyromancer';

            axios.get(`https://api.scryfall.com/cards/random`).then((response) => {
               that.card = response.data;
               console.log(response.data.data)
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