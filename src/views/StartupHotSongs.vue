<template>
    <SongRow>
        <template v-slot:controls>
            <div :class="'button ' + (hotSongsOffset == 0 ? 'button-disabled' : '')" v-on:click="hotPrevious()"><i class="mdi mdi-chevron-left"></i> {{ $t('songrow.navigation.previous' )}}</div>
            <div :class="'button ' + (hotSongs.length < 9 ? 'button-disabled' : '')" v-on:click="hotNext()">{{ $t('songrow.navigation.next' )}} <i class="mdi mdi-chevron-right"></i></div>
        </template>
        <template v-slot:song-list>
            <SongItemPlaceholder
                v-if="isHotSongsLoading"
                v-for="n in 10"
                v-bind:key="n" />
            <SongItem
                v-if="!isHotSongsLoading"
                v-for="song in hotSongs"
                v-bind:key="song.id"
                v-bind="song" />
        </template>
    </SongRow>
</template>

<script>
    import SSAPI from '@/modules/module.api.js';
    import SongRow from '@/components/Song/SongRow.vue';
    import SongItem from '@/components/Song/SongItem.vue';
    import SongItemPlaceholder from '@/components/Song/SongItemPlaceholder.vue';

    export default {
        name: 'Frontpage',
        data: function() {
            return {
                isHotSongsLoading: true,
                hotSongsOffset: 0,
                hotSongs: []
            }
        },
        mounted: function() {
            let ssapi = new SSAPI();
            
            ssapi.getHotThisWeekSongs(this.$data.hotSongsOffset).then((data) => {
                this.$data.isHotSongsLoading = false;
                this.$data.hotSongs = data;
            });
        },
        components: {
            SongRow,
            SongItem,
            SongItemPlaceholder
        },
        methods: {
            hotNext: function() {
                if(this.$data.hotSongs.length > 9) {
                    this.$data.hotSongsOffset++;
                    this.updateHot();
                }
            },
            hotPrevious: function() {
                if(this.$data.hotSongsOffset > 0) {
                    this.$data.hotSongsOffset--;
                    this.updateHot();
                }
            },
            updateHot: function() {
                let ssapi = new SSAPI();
                this.$data.isHotSongsLoading = true;

                ssapi.getHotThisWeekSongs(this.$data.hotSongsOffset).then((data) => {
                    this.$data.isHotSongsLoading = false;
                    this.$data.hotSongs = data;
                });
            }
        }
    }
</script>

<style scoped lang="less">
    .song-row {
        padding: 50px;
    }
</style>