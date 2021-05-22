<template>

 <transition name="fade">
    <div class="modal">
      <div class="modal__backdrop"  @click="close"/>

      <div class="modal__dialog">
        <div class="modal__header">
          <slot name="header"/>
          <button type="button" class="modal__close"  @click="close">
            <svg xmlns="http://www.w3.org/2000/svg" viewBox="0 0 352 512">
              <path
                fill="currentColor"
                d="M242.72 256l100.07-100.07c12.28-12.28 12.28-32.19 0-44.48l-22.24-22.24c-12.28-12.28-32.19-12.28-44.48 0L176 189.28 75.93 89.21c-12.28-12.28-32.19-12.28-44.48 0L9.21 111.45c-12.28 12.28-12.28 32.19 0 44.48L109.28 256 9.21 356.07c-12.28 12.28-12.28 32.19 0 44.48l22.24 22.24c12.28 12.28 32.2 12.28 44.48 0L176 322.72l100.07 100.07c12.28 12.28 32.2 12.28 44.48 0l22.24-22.24c12.28-12.28 12.28-32.19 0-44.48L242.72 256z"
              ></path>
            </svg>
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
  <!-- <transition name="modal-fade">
    <div class="modal">
      <div class="modal__backdrop"
        role="dialog"
        aria-labelledby="modalTitle"
        aria-describedby="modalDescription"
      >
        <header
          class="modal-header"
          id="modalTitle"
        >
          <slot name="header">
            This is the default tile!
          </slot>
          <button
            class="btn-close"
            @click="close"
            aria-label="Close modal"
          >
            x
          </button>
        </header>

        <section
          class="modal-body"
          id="modalDescription"
        >
          <slot name="body">
            This is the default body!
          </slot>
        </section>

        <footer class="modal-footer">
          <slot name="footer">
            This is the default footer!
          </slot>
          <button
            type="button"
            class="btn-green"
            @click="close"
            aria-label="Close modal"
          >
            Close me!
          </button>
        </footer>
      </div>
    </div>
  </transition> -->
</template>

<script>
  export default {
    name: 'Modal',
    methods: {
      close() {
          console.log("I CLOSE THE MODAL")
        this.$emit('close');
      },
    },
  };
</script>

<style lang="scss">

.modal {
  position: fixed;
  top: 0;
  right: 0;
  bottom: 0;
  left: 0;
  z-index: 9;
  overflow-x: hidden;
  overflow-y: auto;
  &__backdrop {
    position: fixed;
    top: 0;
    right: 0;
    bottom: 0;
    left: 0;
    background-color: rgba(0, 0, 0, 0.3);
    z-index: 1;
  }
  &__dialog {
    position: relative;
    width: 600px;
    background-color: #ffffff;
    border-radius: 5px;
    margin: 50px auto;
    display: flex;
    flex-direction: column;
    z-index: 2;
    @media screen and (max-width: 992px) {
      width: 90%;
    }
  }
  &__close {
    width: 30px;
    height: 30px;
  }
  &__header {
    display: flex;
    align-items: flex-start;
    justify-content: space-between;
    padding: 20px 20px 10px;
  }
  &__body {
    padding: 10px 20px 10px;
    overflow: auto;
    display: flex;
    flex-direction: column;
    align-items: stretch;
  }
  &__footer {
    padding: 10px 20px 20px;
  }
}
.fade-enter-active,
.fade-leave-active {
  transition: opacity 0.2s;
}
.fade-enter,
.fade-leave-to {
  opacity: 0;
}
//   .modal-backdrop {
//    position: fixed;
//     top: 0;
//     right: 0;
//     bottom: 0;
//     left: 0;
//     background-color: rgba(0, 0, 0, 0.3);
//     z-index: 1;
//   }

//   .modal {
//     position: fixed;
//     top: 0;
//     right: 0;
//     bottom: 0;
//     left: 0;
//     z-index: 9;
//     overflow-x: hidden;
//     overflow-y: auto;
//   }

//   .modal-header,
//   .modal-footer {
//     padding: 15px;
//     display: flex;
//   }

//   .modal-header {
//     position: relative;
//     border-bottom: 1px solid #eeeeee;
//     color: #4AAE9B;
//     justify-content: space-between;
//   }

//   .modal-footer {
//     border-top: 1px solid #eeeeee;
//     flex-direction: column;
//   }

//   .modal-body {
//     position: relative;
//     padding: 20px 10px;
//   }

//   .btn-close {
//     position: absolute;
//     top: 0;
//     right: 0;
//     border: none;
//     font-size: 20px;
//     padding: 10px;
//     cursor: pointer;
//     font-weight: bold;
//     color: #4AAE9B;
//     background: transparent;
//   }

//   .btn-green {
//     color: white;
//     background: #4AAE9B;
//     border: 1px solid #4AAE9B;
//     border-radius: 2px;
//   }

//   .modal-fade-enter,
//   .modal-fade-leave-to {
//     opacity: 0;
//   }

//   .modal-fade-enter-active,
//   .modal-fade-leave-active {
//     transition: opacity .5s ease;
//   }
</style>
