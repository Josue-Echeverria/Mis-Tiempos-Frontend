<script lang="ts">
    let selled: Record<string, { price: number }> = {};
    let priceInput: HTMLInputElement;
    let showQrModal = false;

    import { TrashBinSolid, CubeSolid, QuestionCircleSolid, PrinterSolid, SearchSolid, EyeSolid, ReceiptSolid, CameraPhotoSolid } from "flowbite-svelte-icons";
    import { sellingMatrix } from '../../stores/UpdateSellMatrix';
    import { prohibitedNumbers } from '../../stores/UpdateSellMatrix';
    import QrModal from './QrModal.svelte';

    function handleNumberKeydown(event: KeyboardEvent) {
        if (event.key === "Enter") {
            event.preventDefault();
            priceInput?.focus();
        }
    }

    function updateSalesData(numbers: string[], price: number) {
        const newSelled = { ...selled };
        numbers.forEach((num) => {
            if (newSelled[num]) {
                newSelled[num].price += price;
            } else {
                newSelled[num] = { price };
            }
        });
        selled = newSelled;
    }

    function deleteNumber(number: string) {
        const { [number]: _, ...rest } = selled;
        selled = rest;
    }

    function onSubmit(event: Event) {
        event.preventDefault();
        const formData = new FormData(event.target as HTMLFormElement);
        const numberInput = formData.get("number") as string;
        const price = formData.get("price") as string;
        
        if (!numberInput || !price) {
            return;
        }

        // Split by plus sign
        let numbers: string[] = numberInput.split('+').map(n => n.trim()).filter(n => n);
        
        // Expand any ranges marked with * (e.g., "1*" becomes 10-19)
        const expandedNumbers: string[] = [];
        numbers.forEach(num => {
            if (num.includes('*')) {
                const digit = num.replace('*', '');
                const baseNum = parseInt(digit) * 10;
                for (let i = 0; i < 10; i++) {
                    console.log(baseNum + i);
                    expandedNumbers.push(String(baseNum + i));
                }
            } else {
                expandedNumbers.push(num);
            }
        });

        // Filter out prohibited numbers
        const validNumbers = expandedNumbers.filter(num => !$prohibitedNumbers.includes(parseInt(num)));

        updateSalesData(validNumbers, parseInt(price));

        (event.target as HTMLFormElement).reset();
    }

    function confirmSale() {
        sellingMatrix.update(matrix => {
            for (const [number, price] of Object.entries(selled)) {
                matrix[number] = (matrix[number] || 0) + price.price;
            }
            return matrix;
        });
        selled = {}; // Clear the local list
    }

    // TODO: Implement functionality for these buttons
    function cleanSell() {
        if (Object.keys(selled).length === 0) {
            return;
        }
        selled = {};
    }

    // TODO: Implement functionality for these buttons
    function generateRandomNumbers() {
    }

    // TODO: Implement functionality for these buttons
    function generatePairs() {
    }

    //TODO: Implement functionality for these buttons
    function printTicket() {
    }

    // TODO: Implement functionality for these buttons
    function searchTicket() {
    }

    // TODO: Implement functionality for these buttons
    function viewQR() {
        if (Object.keys(selled).length === 0) {
            return;
        }
        showQrModal = true;
    }

    // TODO: Implement functionality for these buttons
    function scanQR() {
    }

    // TODO: Implement functionality for these buttons
    function viewTickets() {
    }
</script>

{#if showQrModal}
    <QrModal data={JSON.stringify(selled)} onClose={() => showQrModal = false} />
{/if}

<section class="sell">
    <form on:submit={onSubmit}>
        <div class="question">
            <label for="number">Numero:</label>
            <input
                id="number"
                name="number"
                min="0"
                max="99"
                on:keydown={handleNumberKeydown}
            />
        </div>

        <div class="question">
            <label for="price">Monto:</label>
            <input
                type="number"
                id="price"
                name="price"
                bind:this={priceInput}
            />
        </div>
        <button type="submit">Agregar</button>
    </form>
    <!-- TODO: Implement functionality for these buttons -->
    <div class="buttons-group">
        <button 
            on:click={cleanSell}
            disabled={Object.keys(selled).length === 0}
        >
            <TrashBinSolid class="shrink-0 h-4 w-4" />
            Limpiar
        </button>
        <button on:click={generatePairs}>
            <CubeSolid class="shrink-0 h-4 w-4" />
            Pares
        </button>
        <button on:click={generateRandomNumbers}>
            <QuestionCircleSolid class="shrink-0 h-4 w-4" />
            Aleatorio
        </button>
        <button 
            on:click={printTicket}
            disabled={Object.keys(selled).length === 0}
        >
            <PrinterSolid class="shrink-0 h-4 w-4" />
            Imprimir
        </button>
    </div>
    <div class="selled">
        <table>
            <thead>
                <tr>
                    <th>Numero</th>
                    <th>Monto</th>
                </tr>
            </thead>
            <tbody>
                {#each Object.entries(selled) as [number, price]}
                    <tr>
                        <td>{number}</td>
                        <td>${price.price}</td>
                        <td>
                            <button class="delete-btn" on:click={() => deleteNumber(number)}>X</button>
                        </td>
                    </tr>
                {/each}
            </tbody>
        </table>
        <!-- TODO: Implement functionality for these buttons -->
        <button
            on:click={confirmSale}
            disabled={Object.keys(selled).length === 0}
        >
            Confirmar Venta</button
        >
        <div class="buttons-group">
            <button on:click={searchTicket}>
                <SearchSolid class="shrink-0 h-4 w-4" />
                Buscar Tiquete
            </button>
            <button 
                on:click={viewQR}
                disabled={Object.keys(selled).length === 0}
            >
                <EyeSolid class="shrink-0 h-4 w-4" />
                Ver QR
            </button>
            <button on:click={scanQR}>
                <CameraPhotoSolid class="shrink-0 h-4 w-4" />
                Escanear QR
            </button>
            <button on:click={viewTickets}>
                <ReceiptSolid class="shrink-0 h-4 w-4" />
                Tiquetes
            </button>

        </div>
    </div>
</section>

<style>
    .sell {
        flex-direction: column;
        width: 40%;
        height: 100%;
    }

    .sell form {
        display: flex;
        justify-content: center;
        justify-content: space-between;
        width: 100%;
        gap: 1rem;
    }

    .question {
        display: flex;
        flex-direction: column;
        gap: 0.5rem;
    }

    .question input {
        width: 100%;
    }

    .selled {
        width: 100%;
        flex:1;
    }
    
    .selled table {
        width: 100%;
        border-collapse: collapse;
        margin-top: 1rem;
    }

    .selled th,
    .selled td {
        padding: 0.25rem 0.5rem;
        text-align: left;
        border: 1px solid #ccc;
    }

    .selled th {
        background-color: var(--color-box-background);
        font-weight: 600;
        border: 1px solid #ccc;
        border-bottom: 2px solid #ccc;
    }

    .selled tr:hover {
        background-color: #f9fafb;
    }

    .selled button {
        width: 100%;
    }

    .delete-btn {
        background-color: #dc3545;
        padding: 0.25rem 0rem;
    }

    .delete-btn:hover {
        background-color: #c82333;
    }

    .buttons-group {
        display: flex;
        justify-content: space-between;
        gap: 0.5rem;
        margin: 1rem 0;
        font-size: medium;
    }

</style>
