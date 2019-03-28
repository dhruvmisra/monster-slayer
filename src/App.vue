<template>
    <div class="container">
        <br>
        <h1 class="text-center">Monster Slayer</h1>
        <hr>
        <br>
        <div class="row">
            <div class="col">
                <h1 class="text-center">YOU</h1>
                <br>
                <div class="progress" style="height: 40px">
                    <div class="progress-bar bg-success" :style="{width: playerHealth + '%'}" role="progressbar">{{ playerHealth }}%</div>
                </div>        
            </div>
            <div class="col">
                <h1 class="text-center">MONSTER</h1>
                <br>
                <div class="progress" style="height: 40px">
                    <div class="progress-bar bg-success" :style="{width: monsterHealth + '%'}" role="progressbar">{{ monsterHealth }}%</div>
                </div> 
            </div>
        </div>
        <br>
        <div class="card my-4" v-if="!gameRunning">
            <div class="card-body mx-auto">
                <button @click="newGame()" class="btn btn-lg btn-primary">Start New Game</button>
            </div>
        </div>
        <template v-else>
            <div class="card my-4">
                <div class="card-body mx-auto" style="height: 100px;">
                    <button @click="attack()" class="btn btn-warning mx-2">Attack</button>
                    <button @click="specialAttack()" class="btn btn-danger mx-2">Special Attack</button>
                    <button @click="heal()" class="btn btn-success mx-2">Heal</button>
                    <button @click="quit()" class="btn btn-secondary mx-2">Quit</button>
                </div>
            </div>
            <div class="card col-sm-6 mx-auto">
                <div class="card-body">
                    <ul class="list-group">
                        <transition-group name="slide">
                            <li class="list-item my-1 text-center" 
                            v-for="turn in turns" 
                            :key="turn.id"
                            :class="[{player: turn.isPlayer}, {monster: !turn.isPlayer}]"
                            >{{ turn.text }}
                            </li>
                        </transition-group>
                    </ul>
                </div>
            </div>
        </template>
    </div>

</template>

<script>
    export default {
        data() {
            return {
                playerHealth: 100,
                monsterHealth: 100,
                gameRunning: false,
                turns: [],
                currentTurn: 0
            };
        },
        methods: {
            newGame() {
                this.gameRunning = true;
                this.playerHealth = 100;
                this.monsterHealth = 100;
                this.turns = [];
            },
            quit() {
                this.gameRunning = false;
            },
            calcDamage(min, max) {
                return Math.max(Math.floor((Math.random() * max) + 1, min));
            },
            monsterAttacks() {
                let damage = this.calcDamage(5, 10);
                this.playerHealth -= damage;
                this.turns.unshift({
                    isPlayer: false,
                    text: 'Monster attacks Player for ' + damage,
                    id: this.currentTurn + 1
                });
                this.currentTurn++;
                this.checkWin();
            },
            playerAttacks(min, max) {
                let damage = this.calcDamage(min, max);
                this.monsterHealth -= damage;
                this.turns.unshift({
                    isPlayer: true,
                    text: 'Player attacks Monster for ' + damage,
                    id: this.currentTurn + 1
                });
                this.currentTurn++;
            },
            attack() {
                this.playerAttacks(5, 10);
                this.monsterAttacks();
            },
            specialAttack() {
                this.playerAttacks(10, 20);
                this.monsterAttacks();
            },
            heal() {
                if(this.playerHealth <= 90) {
                    this.playerHealth += 10;
                } else {
                    this.playerHealth = 100;
                }
                this.turns.unshift({
                    isPlayer: true,
                    text: 'Player heals for 10',
                    id: this.currentTurn + 1
                });
                this.currentTurn++;
                this.monsterAttacks();
            },
            checkWin() {
                if(this.monsterHealth <= 0) {    
                    if(confirm('You Won. New Game?'))
                        this.newGame();
                    else
                        this.quit();
                } else if(this.playerHealth <= 0) {
                    if(confirm('You lost. New Game?'))
                        this.newGame();
                    else
                        this.quit();
                }
            }
        }
    }
</script>

<style>
    .player {
        color: white;
        background-color: rgb(0, 170, 0);
    }

    .monster {
        color: white;
        background-color: rgb(189, 60, 0)
    }
    li {
        height: 30px;
    }

    .slide-enter {
        opacity: 0;
    }
    .slide-enter-active {
        animation: slide-in 0.5s ease-out forwards;
    }
    .slide-move {
        transition: all 0.3s;
    }

    @keyframes slide-in {
        from {
            opacity: 0;
            transform: translateY(-20px);
        }
        to {
            opacity: 1;
            transform: translateY(0);
        }
    }
</style>
