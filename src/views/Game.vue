<template>
    <div class="game">
        <p>Play Game</p>
        <p @click="newGame">New Game</p>
        <ul class="clearfix">
            <li v-for="(n, index) in npc"
                :key="index"
                :class="{'red': index < 6, 'blue': index >= 6, 'row-1': index < 5, 'row-2': index === 5 || index === 11, 'row-3': 5 < index && index < 11}"
                @click="play(index, index)"
            >({{ n.num }})
            </li>
        </ul>
        <div>
            <span>Red: {{ red }}</span> |
            <span>Blue: {{ blue }}</span>
        </div>
    </div>
</template>

<script>
    export default {
        name: 'page',
        data() {
            return {
                users: 2,
                npc: [],
                red: 0,
                blue: 0
            }
        },
        created() {
            this.initGame()
        },
        methods: {
            initGame() {
                while (this.npc.length < 12) {
                    this.npc.push({num: 5})
                }
            },
            newGame() {
                this.red = 0
                this.blue = 0
                this.npc.forEach(n => {
                    n.num = 5
                })
            },

            run(i) {
                let cur_num = this.npc[i].num
                this.npc[i].num = 0
                while (cur_num > 0) {
                    i = this.nextIndex(i)
                    this.npc[i].num += 1
                    cur_num--
                }
                return this.nextIndex(i)
            },
            play(i, u) {
                let user = u < 6 ? 'red' : 'blue'
                i = this.run(i)
                if (this.npc[i].num > 0) {
                    this.play(i, u)
                } else {
                    this[user] += this.npc[this.nextIndex(i)].num
                    this.npc[this.nextIndex(i)].num = 0
                }
            },

            nextIndex(i) {
                return i === 11 ? 0 : i + 1
            }
        }
    }
</script>

<style lang="scss" scoped>
    ul {
        position: relative;
        padding: 0;
        width: 300px;
        margin: 40px auto;
        li {
            display: inline-block;
            width: 60px;

            &:last-child {
                margin-right: 0;
            }

            &.row-1 {
                float: left;
                position: relative;
            }
            &.row-2 {
                position: absolute;
                top: 22px;
                &:first-child {
                    right: 0;
                }
                &:last-child {
                    left: -60px;
                }
            }
            &.row-3 {
                float: right;
                position: relative;
                top: 25px;
            }
        }
        li.red {
            color: #ea5b5b;
        }
        li.blue {
            color: #0d47a1;
        }
    }
</style>
