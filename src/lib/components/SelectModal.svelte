<script lang="ts">
    import { Dropdown, Button, DropdownItem, DropdownGroup, Radio } from 'flowbite-svelte';
    import { ChevronDownOutline } from "flowbite-svelte-icons";

    export let title = "";
    export let items: string[] = [];
    export let selectedValue: string = "";
    export let placeholder = "Select an option";
    export let maxHeight = "200px";

    function handleRadioChange(value: string) {
        selectedValue = value;
    }

    $: displayText = selectedValue || placeholder;
</script>

<div class="dropdown-container">
    <Button slot="trigger" class="flex">
        {title}
        <ChevronDownOutline class="ms-2 h-6 w-6 text-white dark:text-white" />
    </Button>
    <Dropdown simple class="h-48 w-30 overflow-y-auto py-1">
        <DropdownGroup>
            {#each items as item, i (item)}
            <DropdownItem class="flex items-center gap-2 text-base font-semibold">
                <Radio 
                        name="{title}-radio-group"
                        value={item}
                        bind:group={selectedValue}
                        class="mr-2 text-sm text-gray-700"
                    >
                        {item}
                    </Radio>
                </DropdownItem>
            {/each}
        </DropdownGroup>
        {#if items.length === 0}
            <DropdownItem class="empty-state">
                No options available
            </DropdownItem>
        {/if}
    </Dropdown>
</div>
