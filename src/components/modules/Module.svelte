<script lang="ts">
  import { ui_store } from "../../stores/store";
  import Timeline from "./Timeline.svelte";
  import Map from "./Map.svelte";
  import MediumMediaFile from "./MediumMediaFile.svelte";

  export let module;
  export let medium;

  let modules_options = {
    media: MediumMediaFile,
    map: Map,
    timeline: Timeline,
  };

  function close_module(module, medium) {
    // if X on a medium and there is more than one in view
    // just remove medium from view
    if (module.includes("medi") && $ui_store.media_in_view.length > 1) {
      $ui_store.media_in_view = $ui_store.media_in_view.filter(
        (exist_UAR) => exist_UAR !== medium.UAR
      );
    } else {
      $ui_store.modules_in_view = $ui_store.modules_in_view.filter(
        (module_in_view) => module_in_view !== module
      );
      if (module.includes("medi")) {
        $ui_store.media_in_view = $ui_store.media_in_view.filter(
          (exist_UAR) => exist_UAR !== medium.UAR
        );
      }
    }
  }

  function toggle_form() {
    $ui_store.open_form.includes(medium.UAR) ? $ui_store.open_form = $ui_store.open_form.filter(elem=> elem != medium.UAR) : $ui_store.open_form = [...$ui_store.open_form, medium.UAR]
  }
</script>

<div
  class="box module {module}_module"
  style="display:{$ui_store.modules_in_view.includes(module) ? 'flex' : 'none'}"
>
  <div class="module_topbar">
    <div class="module_title text_level1">
      {module}
      {#if module.includes("medi")}
        : <div class="media_file_name">{medium?.UAR}</div>
        <button class="media_file_form_button" on:click={() => toggle_form()}
          >Toggle form</button
        >
      {/if}
    </div>
    <div
      class="module_close"
      on:pointerdown={(e) => e.stopPropagation()}
      on:click={() => close_module(module, medium)}
    >
      &#215;
    </div>
  </div>
  <div class="module_content">
    <svelte:component this={modules_options[module]} {medium} />
  </div>
</div>

<style>
  .module {
    display: flex;
    flex-flow: column nowrap;
    flex: 1 1 auto;
    margin-bottom: var(--grid-size);
  }

  .module_topbar {
    width: 100%;
    display: flex;
    flex-flow: row nowrap;
    justify-content: flex-start;
    justify-items: stretch;
    align-items: center;
  }

  .module_topbar > * {
    line-height: calc(2 * var(--grid-size));
    height: calc(2 * var(--grid-size));
    width: calc(2 * var(--grid-size));
  }

  .module_title {
    flex-grow: 2;
    margin-left: calc(0.5 * var(--grid-size));
  }

  .module_close {
    cursor: pointer;
  }

  .module_content {
    flex-grow: 2;
    min-width: 0;
    min-height: 0;
  }

  .media_module:not(:first-child) {
    margin-left: var(--grid-size);
  }

  .timeline_module {
    margin-right: var(--grid-size);
  }

  .media_file_form_button {
    background-color: black;
    border-radius: 7px;
    color: white;
    border: white solid 1px;
    margin-left: 10px;
  }

  :global(.media_file_name) {
    display: inline-block;
    background: white;
    border-radius: 4px;
  }
</style>

