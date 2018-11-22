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
        <div><span>Remain num: {{ remain_num }}</span></div>
        <div>
            <span>Red(owned): {{ red }}</span> |
            <span>Blue(owned): {{ blue }}</span>
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
                remain_num: 0,
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

            play(i, u) {
                let user = u < 6 ? 'red' : 'blue'

                this.run(i).then(r => {
                    i = r
                    if (this.npc[i].num > 0) {
                        this.play(i, u)
                    } else {
                        this[user] += this.npc[this.nextIndex(i)].num
                        this.npc[this.nextIndex(i)].num = 0
                    }
                })
            },

            run(i) {
                let cur_num = this.npc[i].num
                let r_i = i + cur_num

                this.npc[i].num = 0

                return new Promise((resolve) => {
                    this.remain_num = cur_num

                    for (let j = 0; j < cur_num; j++) {
                        let that = this
                        ;(function (j) {
                            let timer = setTimeout(() => {
                                that.remain_num = cur_num - j - 1

                                i = that.nextIndex(i)
                                that.npc[i].num += 1

                                if (j === cur_num-1) {
                                    setTimeout(() => {
                                        resolve(that.nextIndex(r_i))
                                    }, 1000)
                                }

                                clearTimeout(timer)
                            }, j * 1000)
                        })(j)
                    }
                })
            },

            nextIndex(i) {
                return (i + 1) % 12
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
