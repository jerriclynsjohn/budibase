<script>
  import { goto } from "@sveltech/routify"
  import { backendUiStore } from "builderStore"
  import { TableNames } from "constants"
  import EditTablePopover from "./popovers/EditTablePopover.svelte"
  import EditViewPopover from "./popovers/EditViewPopover.svelte"
  import { Switcher } from "@budibase/bbui"
  import NavItem from "components/common/NavItem.svelte"

  $: selectedView =
    $backendUiStore.selectedView && $backendUiStore.selectedView.name

  function selectTable(table) {
    backendUiStore.actions.tables.select(table)
    $goto(`./table/${table._id}`)
  }

  function selectView(view) {
    backendUiStore.actions.views.select(view)
    $goto(`./view/${view.name}`)
  }

  function onClickView(table, viewName) {
    if (selectedView === viewName) {
      return
    }
    selectView({
      name: viewName,
      ...table.views[viewName],
    })
  }
</script>

{#if $backendUiStore.selectedDatabase && $backendUiStore.selectedDatabase._id}
  <div class="hierarchy-items-container">
    {#each $backendUiStore.tables as table, idx}
      <NavItem
        border={idx > 0}
        icon={`ri-${table._id === TableNames.USERS ? 'user' : 'table'}-line`}
        text={table.name}
        selected={selectedView === `all_${table._id}`}
        on:click={() => selectTable(table)}>
        {#if table._id !== TableNames.USERS}
          <EditTablePopover {table} />
        {/if}
      </NavItem>
      {#each Object.keys(table.views || {}) as viewName}
        <NavItem
          indentLevel={1}
          icon="ri-eye-line"
          text={viewName}
          selected={selectedView === viewName}
          on:click={() => onClickView(table, viewName)}>
          <EditViewPopover
            view={{ name: viewName, ...table.views[viewName] }} />
        </NavItem>
      {/each}
    {/each}
  </div>
{/if}
