<script>
  import { componentStore } from "stores/builder"
  import { ActionMenu, MenuItem, Icon, notifications } from "@budibase/bbui"

  export let component

  $: definition = componentStore.getDefinition(component?._component)
  $: noPaste = !$componentStore.componentToPaste

  // "editable" has been repurposed for inline text editing.
  // It remains here for legacy compatibility.
  // Future components should define "static": true for indicate they should
  // not show a context menu.
  $: showMenu = definition?.editable !== false && definition?.static !== true

  const storeComponentForCopy = (cut = false) => {
    componentStore.copy(component, cut)
  }

  const pasteComponent = mode => {
    try {
      componentStore.paste(component, mode)
    } catch (error) {
      notifications.error("Error saving component")
    }
  }
</script>

{#if showMenu}
  <ActionMenu>
    <div slot="control" class="icon">
      <Icon size="S" hoverable name="MoreSmallList" />
    </div>
    <MenuItem
      icon="Copy"
      keyBind="Ctrl+C"
      on:click={() => storeComponentForCopy(false)}
    >
      Copy
    </MenuItem>
    <MenuItem
      icon="LayersSendToBack"
      keyBind="Ctrl+V"
      on:click={() => pasteComponent("inside")}
      disabled={noPaste}
    >
      Paste
    </MenuItem>
  </ActionMenu>
{/if}

<style>
  .icon {
    display: grid;
    place-items: center;
  }
</style>
