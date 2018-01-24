<template>
  <div class="training">
    <h1>Math training. Level {{ level + 1 }}</h1>
    <hr>

    <div class="progress">
      <div class="progress-bar" :style="progressStyles"></div>
    </div>

    <div class="box">
      <transition name="flip" mode="out-in">
        <app-start-screen v-if="state == 'start'" @onStart="onStart">
        </app-start-screen>

        <app-question v-else-if="state == 'question'"
                      @success="onQuestSuccess"
                      @error="onQuestError"
                      :settings="levels[level]"
        ></app-question>

        <app-message v-else-if="state == 'message'"
                     :type="message.type"
                     :text="message.text"
                     @onNext="onNext"
        ></app-message>

        <app-result-screen v-else-if="state == 'result'"
                           :stats="stats"
                           @repeat="onStart"
                           @nextLevel="onNextLevel"
        ></app-result-screen>
        <div v-else> Unknow state</div>

      </transition>
    </div>
  </div>
</template>

<script>
  export default {
    name: 'app',


    data() {
      return {
        state: 'start',
        stats: {
          success: 0,
          error: 0,
          complete: 'Next level'
        },
        message: {
          type: '',
          text: ''
        },
        questMax: 3,
        level: 0,
        levels: [
          {
            from: 10,
            to: 40,
            range: 5,
            variants: 2
          },
          {
            from: 100,
            to: 200,
            range: 20,
            variants: 4
          },
          {
            from: 500,
            to: 900,
            range: 40,
            variants: 6
          }
        ]
      }
    },
    computed: {
      questDone() {
        return this.stats.success + this.stats.error;
      },
      progressStyles() {
        return {
          width: (this.questDone / this.questMax * 100) + '%'
        };
      }
    },
    methods: {
      onStart() {
        this.state = 'question';
        this.stats.success = 0;
        this.stats.error = 0;
      },
      onQuestSuccess() {
        this.state = 'message';
        this.message.text = 'Good Job!';
        this.message.type = 'success';
        this.stats.success++;
      },
      onQuestError(msg) {
        this.state = 'message';
        this.message.text = msg;
        this.message.type = 'warning';
        this.stats.error++;
      },
      onNext() {
        if (this.questDone < this.questMax) {
          this.state = 'question';
        } else {
          if (this.level >= this.levels.length - 1) {
            this.stats.complete = 'begin again'
          }
          this.state = 'result';
        }
      },
      onNextLevel() {
        if (this.stats.complete === 'begin again') {
          this.level = 0;
        } else {
          this.level++;
        }
        this.onStart();
      }
    }
  }
</script>

<style scoped>
  .training {
    max-width: 700px;
    margin: 20px auto;
  }

  .flip-enter-active {
    animation: flipInX 0.3s ease-in-out;
  }

  .flip-leave-active {
    animation: flipOutX 0.3s ease-in-out;

  }

  @keyframes flipInX {
    from {
      transform: rotateX(90deg);
    }
    to {
      transform: rotateX(0deg);
    }
  }

  @keyframes flipOutX {
    from {
      transform: rotateX(0deg);
    }
    to {
      transform: rotateX(90deg);
    }
  }

  .progress-bar {
    transition: width 0.5s ease;
  }

  .box {
    margin: 20px 0;
  }
</style>
