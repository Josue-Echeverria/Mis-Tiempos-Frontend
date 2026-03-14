<script lang="ts">
    import {
        Table,
        TableBody,
        TableBodyCell,
        TableBodyRow,
        TableHead,
        TableHeadCell,
    } from "flowbite-svelte";

    interface Header {
        key: string;
        label: string;
    }

    export let headers: Header[] = [];
    export let rows: Record<string, unknown>[] = [];
</script>

<section>
    <Table>
        <TableHead>
            {#each headers as header (header.key)}
                <TableHeadCell>{header.label}</TableHeadCell>
            {/each}
        </TableHead>
        <TableBody>
            {#each rows as row (row.id)}
                <TableBodyRow>
                    {#each headers as header (header.key)}
                        <TableBodyCell>{row[header.key]}</TableBodyCell>
                    {/each}
                </TableBodyRow>
            {/each}
        </TableBody>
    </Table>
</section>
