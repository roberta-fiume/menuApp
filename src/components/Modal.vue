<template>
 <transition name="fade">
    <div class="modal">
      <div class="modal__backdrop" @click="close"/>

      <div class="modal__dialog">
        <div class="modal__header">
          <slot name="header"/>
          <button type="button" class="modal__close"  @click="close">
             <i class="fa fa-times modal__close-icon"></i>
          </button>
        </div>

        <div class="modal__body">
          <slot name="body"/>
        </div>

        <div class="modal__footer">
          <slot name="footer"/>
        </div>
      </div>
    </div>
  </transition>
</template>

<script>
  export default {
    name: 'Modal',
    methods: {
      close() {
        this.$emit('close');
      },
    },
  };
</script>

<style lang="scss">
@import "../styles/variables.scss";
@import "../styles/_queries.scss";
@import "../styles/mixins.scss";

.modal {
  @include positionProperties(fixed, 0, 0, 0, 0);
  z-index: 9;
  overflow-x: hidden;
  overflow-y: auto;

  &__backdrop {
    @include positionProperties(fixed, 0, 0, 0, 0);
    background-color: rgba(0, 0, 0, 0.3);
    z-index: 1;
  }

  &__dialog {
    position: relative;
    width: 600px;
    background-color: $white;
    margin: 50px auto;
    display: flex;
    flex-direction: column;
    z-index: 2;
    @include borderProperties($secondary, 8px);

    @include responsive(tablet) {
      width: 400px;  
    }

    @include responsive(mobile) {
      width: 300px;  
    }

  }

  &__close {
   cursor: pointer;
   @include borderProperties($secondary-dark-title, 3px);
   @include measures(25px, 25px);
   @include flexProperties(column, center, center);

    &-icon {
       font-size: 15px;
       color: $secondary-dark-title;
    }
  }

  &__header {
     @include flexProperties(row, space-between, flex-start);
    padding: 20px 20px 10px;
  }

  &__body {
    padding: 10px 20px 10px;
    overflow: auto;
     @include flexProperties(column, center, stretch);
  }

}


</style>
