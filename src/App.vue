<template>
  <div id="app">
    <div class="container text-center">
      <div class="row">
        <div class="col-xs-12 col-sm-8 offset-sm-2 col-md-6 col-md-offset-3">
          <h1 class="text-center">Matematički Kviz</h1>
        </div>
      </div>
      <hr />
      <transition-group name="slide" mode="in-out">
        <h4 v-if="elapsedTime" class="alert alert-danger" key="alert">
          Vreme:
          <strong>{{elapsedTime.toFixed(2)/100}}</strong>s
        </h4>

        <div class="row" key="row">
          <div class="col-xs-12 col-sm-8 col-md-3">
            <transition-group name="slide" mode="out-in">
              <div v-if="counter > 2 && counter <= 4" key="math">
                <img class="img-responsive" src="./assets/math.png" alt />
              </div>

              <div v-if="counter > 4 && counter <= 14" key="congrats">
                <img class="img-responsive" src="./assets/congrats.jpg" alt />
              </div>

              <div v-if="counter > 14" key="wizard">
                <img class="img-responsive" src="./assets/wizard.gif" alt />
              </div>
            </transition-group>
          </div>
          <div class="col-xs-12 col-sm-8 col-offset-sm-2 col-md-6">
            <transition name="flip" mode="out-in">
              <component :is="mode" @answered="answered($event)" @confirmed="mode= 'appQuestions'"></component>
            </transition>
            <hr />
            <h4>
              Trenutni rezultat:
              <strong>{{counter}}p</strong>
            </h4>
          </div>
          <div class="col-xs-12 col-sm-8 col-md-3 col-offset-md-9" v-if="previousScore.length > 0">
            <h3>Prethodni rezultati:</h3>
            <ul class="list-group">
              <transition-group name="slide">
                <li
                  v-for="(rezultat, index) in previousScore"
                  :key="rezultat.id"
                  class="list-group-item"
                  :class="{'alert-danger': rezultat.time > 3 || rezultat.time == '0.00', 'alert-success': rezultat.time < 3}"
                >
                  {{index + 1 }}.
                  <strong style="font-size: 20px">{{rezultat.score}}</strong>p ---
                  <span
                    :class="{'alert-danger': rezultat.time > 3 || rezultat.time == '0.00', 'alert-success': rezultat.time < 3}"
                  >
                    Prosečno vreme:
                    <strong>{{ rezultat.time }}s</strong>
                  </span>
                </li>
              </transition-group>
            </ul>
          </div>
        </div>
      </transition-group>
    </div>
  </div>
</template>

<script>
import Answers from "./components/Answers";
import Questions from "./components/Questions";
export default {
  name: "App",
  components: {
    appAnswers: Answers,
    appQuestions: Questions
  },
  data() {
    return {
      mode: "appQuestions",
      counter: 0,
      elapsedTime: 0,
      timer: undefined,
      id: 0,
      previousScore: []
    };
  },

  methods: {
    answered(isCorrect) {
      if (isCorrect) {
        this.mode = "appAnswers";
        this.counter++;

        if (this.counter == 1) {
          this.timer = setInterval(() => {
            this.elapsedTime += 10;
          }, 100);
          console.log("started");
        }
      } else {
        let avgTime;
        this.id++;
        this.mode = "appQuestions";
        alert("Wrong, try again.");

        if (this.counter == 0) {
          avgTime = 0;
        } else {
          avgTime = this.elapsedTime / 100 / this.counter;
        }

        this.previousScore.unshift({
          score: this.counter,
          time: avgTime.toFixed(2),
          id: this.id
        });

        this.counter = 0;
        clearInterval(this.timer);
        this.elapsedTime = 0;
      }
    }
  }
};
</script>



<style>
.flip-enter {
  /* transform: rotateY(0deg) pocetno stanje. komentarisano jer je ono takvo (pocetno) po defaultu pa nam fakticki ni ne treba*/
}
.flip-enter-active {
  animation: flip-in 0.2s ease-out forwards;
}
.flip-leave {
  /* transform: rotateY(0deg) */
}
.flip-leave-active {
  animation: flip-out 0.2s ease-out forwards;
}
@keyframes flip-out {
  from {
    transform: rotateY(0deg);
  }
  to {
    transform: rotateY(90deg);
  }
}
@keyframes flip-in {
  from {
    transform: rotateY(90deg);
  }
  to {
    transform: rotateY(0deg);
  }
}

.slide-enter {
  opacity: 0;
}
.slide-enter-active {
  animation: slide-in 0.2s ease-in-out forwards;
  transition: opacity 1;
}
.slide-leave {
}
.slide-leave-active {
  animation: slide-out 1s ease-in-out forwards;
  transition: opacity 1;
  opacity: 0;
  position: absolute;
}
.slide-move {
  transition: transform 0.2s;
}

@keyframes slide-in {
  from {
    transform: translateY(20px);
  }
  to {
    transform: translateY(0);
  }
}
@keyframes slide-out {
  from {
    transform: translateX(0);
  }
  to {
    transform: translateX(-50px);
  }
}
</style>
