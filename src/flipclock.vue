/*
 * 翻页时钟组件
 * @Author: liangzc 
 * @Date: 2018-06-08 10:19:55 
 * @Last Modified by: liangzc
 * @Last Modified time: 2018-06-30 13:37:04
 */
 <template>
  <div ref="flipclock"
    class="flip-clock" />
</template>
<script>
//配置请参考 http://flipclockjs.com/
import FlipClock from './flipclock.module.min';
export default {
  name: 'flip-clock',
  props: {
    /**
     * An integer used to start the clock (no. seconds)
     */
    digit: Number,
    /**
     * An object of properties to override the default
     * {  time: Number, //时间，单位秒
     *    label: Boolean, //是否显示时间label
     *    dot: Boolean, //是否显示时间节点间隔的点
     *    divider: {
     *      days: String, //天与小时节点之间的分割
     *      hours: String, //小时与分钟节点之间的分割
     *      minutes: String, //分钟与秒节点之间的分割
     *      seconds: String, //秒钟单位
     *    }
     *    ... 其他官方属性
     * }
     */
    options: {
      type: Object,
      default: function() {
        return {};
      }
    }
  },
  data: function() {
    return {
      clock: null,
      convert: {
        days: 'hours',
        hours: 'minutes',
        minutes: 'seconds',
        seconds: 'last'
      }
    };
  },
  created: function() {
    this.$nextTick(() => {
      this.init(this.options);
    });
  },
  watch: {
    options: {
      handler: function(val) {
        this.reset(val);
      },
      deep: true
    },
    digit: function(val) {
      console.warn('deprecated. please use `options.digit` instead');
    }
  },
  methods: {
    init: function(options) {
      options = options || {};
      this.destroyClock();
      this.clock = new FlipClock(
        this.$refs.flipclock,
        options.digit !== undefined ? options.digit : this.digit,
        Object.assign({}, options, {
          autoStart: options.hasOwnProperty('autoStart') ?
            options.autoStart :
            true
        })
      );
      if (
        options.divider &&
        Object.prototype.toString.call(options.divider) === '[object Object]'
      ) {
        for (var key in options.divider) {
          var el = document.querySelector(
            '.flip-clock-divider.VAR'.replace('VAR', this.convert[key])
          );
          if (el) {
            el.innerHTML = options.divider[key];
          } else if (this.convert[key] === 'last') {
            this.$refs.flipclock.appendChild(
              FlipClock.Base.createDom(
                '<span class="flip-clock-divider">VAR</span>'.replace('VAR', options.divider[key])
              )
            );
          }
        }
      }
      options.time && this.clock.setTime(options.time);
      options.time && this.clock.autoStart && this.clock.start();
    },
    instance: function() {
      return this.clock;
    },
    trigger: function(event, params) {
      this.clock && this.clock[event] && this.clock[event](arguments.slice(1));
    },
    start: function(callback) {
      this.clock && this.clock.start(callback);
    },
    stop: function(callback) {
      this.clock && this.clock.stop(callback);
    },
    reset: function(options, callback) {
      if (typeof options === 'function') {
        callback = options;
        options = null;
      }
      this.clock && this.clock.reset(callback);
      if (options) {
        options.digit = options.digit !== undefined ? options.digit : 0;
        this.init(options);
      }
    },
    increment: function() {
      this.clock && this.clock.increment();
    },
    decrement: function() {
      this.clock && this.clock.decrement();
    },
    loadClockFace: function(name, options) {
      this.clock && this.clock.loadClockFace(name, options);
    },
    loadLanguage: function(name) {
      this.clock && this.clock.loadLanguage(name);
    },
    setCountdown: function(value) {
      this.clock && this.clock.setCountdown(value);
    },
    getTime: function() {
      return this.clock ? this.clock.getTime().time : 0;
    },
    setTime: function(value) {
      this.clock && this.clock.setTime(value);
    },
    setOptions: function(options) {
      this.clock && this.clock.setOptions(options);
    },
    destroyClock: function() {
      if (this.clock) {
        this.clock.stop();
        this.clock = null;
      }
    }
  },
  beforeDestroy: function() {
    this.destroyClock();
  }
};
</script>
<style lang="scss">
@import './flipclock.css';
</style>

 
