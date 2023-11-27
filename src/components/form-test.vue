<template>
  <label for="story">Description:</label>

  <br/>
  <br/>

  <div contenteditable="true" class="noselect example" id="story" spellcheck="false"></div>

  <br/>
  <br/>
  <button type="button">Submit</button>

  <div>Total Leave Page: {{ listStatus }}</div>
</template>

<script>
import { onBeforeUnmount, onMounted, reactive, ref } from 'vue'
export default {
  setup() {
    const listStatus =  ref(0)
    const isPageActive = ref(false)
    let interval;
    let preventSelRecursion;

    const onCancelContextMenu = (e) => {
      e.preventDefault();
    }

    const debounce = (fn, delay) => {
      let timer = null;
      return () => {
        clearTimeout(timer);
        timer = setTimeout(() => {
          fn.apply();
        }, delay);
      };
    }

    const disableTextareaCallout = debounce(() => {
      preventSelRecursion = true;
      const s = window.getSelection();

      if (((s === null || s === void 0 ? void 0 : s.rangeCount) || 0) > 0) {
        const r = s === null || s === void 0 ? void 0 : s.getRangeAt(0);
        s === null || s === void 0 ? void 0 : s.removeAllRanges();
        if (r) {
          r.collapse(true)
        }

        setTimeout(() => {
          s === null || s === void 0 ? void 0 : s.addRange(r);
        });
      }
      setTimeout(() => {
        preventSelRecursion = false;
      }, 10);
    },100);

    const onSelectionChange = () => {
      const activeElement = document.activeElement;
      if (activeElement && activeElement.tagName === "DIV" && activeElement.hasAttribute("contenteditable")) {
        if (preventSelRecursion) return;
        disableTextareaCallout();
      }
    }

    const disableIosSafariCallout = () => {
      const activeElement = document.activeElement
      if (!(activeElement && activeElement.tagName === "DIV" && activeElement.hasAttribute("contenteditable"))) {
        const s = window.getSelection();

        if (((s === null || s === void 0 ? void 0 : s.rangeCount) || 0) > 0) {
          const r = s === null || s === void 0 ? void 0 : s.getRangeAt(0);
          s === null || s === void 0 ? void 0 : s.removeAllRanges();

          setTimeout(() => {
            s === null || s === void 0 ? void 0 : s.addRange(r);
          }, 5);
        }
      }
    };

    function detectMob() {
      const toMatch = [
        /Android/i,
        /webOS/i,
        /iPhone/i,
        /iPad/i,
        /iPod/i,
        /BlackBerry/i,
        /Windows Phone/i
      ];

      return toMatch.some((toMatchItem) => {
        return navigator.userAgent.match(toMatchItem);
      });
    }


    const handleVisibilityChange = () => {
      if (isPageActive.value === document.hasFocus()) return;

      isPageActive.value = document.hasFocus();

      if(!isPageActive.value){
        listStatus.value = listStatus.value + 1
        console.log("Page Inactive")
      }
    };


    onMounted(()=> {
      document.addEventListener("touchend", disableIosSafariCallout);
      document.addEventListener('selectionchange', onSelectionChange, false);
      document.addEventListener("contextmenu", onCancelContextMenu, false);
      document.addEventListener("copy", onCancelContextMenu, false);
      document.addEventListener("paste", onCancelContextMenu, false);
      document.addEventListener("cut", onCancelContextMenu, false);

      interval = setInterval(handleVisibilityChange, 600);

      console.log(detectMob())

    });

    onBeforeUnmount(()=> {
      clearInterval(interval);

      document.removeEventListener("touchend", disableIosSafariCallout);
      document.removeEventListener('selectionchange', onSelectionChange, false);
      document.removeEventListener("contextmenu", onCancelContextMenu, false);
      document.removeEventListener("copy", onCancelContextMenu, false);
      document.removeEventListener("paste", onCancelContextMenu, false);
      document.removeEventListener("cut", onCancelContextMenu, false);
    })

    return {
      listStatus,
    }

  },
}
</script>

