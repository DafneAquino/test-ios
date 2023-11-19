<template>
    <label for="story">Description:</label>

    <br/>
    <br/>

    <textarea class="noselect" id="story" name="story" rows="5" cols="33"></textarea>

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

      const onCancelContextMenu = (e) => {
        e.preventDefault();
      }

      const disableIosSafariCallout = (e) => {
        const s = window.getSelection();

        if (((s === null || s === void 0 ? void 0 : s.rangeCount) || 0) > 0) {
          const r = s === null || s === void 0 ? void 0 : s.getRangeAt(0);
          s === null || s === void 0 ? void 0 : s.removeAllRanges();

          setTimeout(() => {
            s === null || s === void 0 ? void 0 : s.addRange(r);
          }, 50);
        }
      }

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
        document.addEventListener("touchend", disableIosSafariCallout)
        document.addEventListener("contextmenu", onCancelContextMenu, false);
        document.addEventListener("copy", onCancelContextMenu, false);
        document.addEventListener("paste", onCancelContextMenu, false);
        document.addEventListener("cut", onCancelContextMenu, false);

        interval = setInterval(handleVisibilityChange, 600);

        console.log(detectMob())

      });

      onBeforeUnmount(()=> {
        clearInterval(interval);

        document.removeEventListener("touchend", disableIosSafariCallout)
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

