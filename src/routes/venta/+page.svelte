<script>
    import Matrix from "$lib/components/venta/Matrix.svelte";
    import Sell from "$lib/components/venta/Sell.svelte";
    import SellHeader from "$lib/components/venta/SellHeader.svelte";
    import { prohibitedNumbers } from "../../lib/stores/UpdateSellMatrix";

    prohibitedNumbers.set([3, 7, 13, 17, 23, 27]); // TODO Ejemplo de números prohibidos
</script>

<section class="sell-container">
    <SellHeader />
    <section class="set-section">
        <Sell />
        <Matrix 
            rows={20}
            columns={5}
        />
    </section>
</section>

<style>
    .sell-container {
        display: flex;
        flex-direction: column;
        gap: 2rem;
        flex: 1;
    }
    
    .set-section {
        gap: 1rem;
        width: 100%;
    }
</style>