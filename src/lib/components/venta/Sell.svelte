<script lang="ts">
    let selled = {} as { number: string; price: number };
    let priceInput: HTMLInputElement;
    import { sellingMatrix } from '../../stores/UpdateSellMatrix';

    function handleNumberKeydown(event: KeyboardEvent) {
        if (event.key === "Enter") {
            event.preventDefault();
            priceInput?.focus();
        }
    }

    function updateSalesData(numbers: string[], price: number) {
        numbers.forEach(number => {
            if (selled[number]) {
                selled[number] += price;
            } else {
                selled = { ...selled, [number]: price };
            }
            
            // Update the matrix
            console.log(number, price);
            sellingMatrix.update(matrix => {
                matrix[number] = (matrix[number] || 0) + price;
                return matrix;
            });
        });
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

        updateSalesData(expandedNumbers, parseInt(price));

        console.log(sellingMatrix);
        
        // Clear the form
        (event.target as HTMLFormElement).reset();
    }
</script>

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
                        <td>${price}</td>
                    </tr>
                {/each}
            </tbody>
        </table>
        <button
            on:click={() => alert("Venta confirmada!")}
            disabled={Object.keys(selled).length === 0}
        >
            Confirmar Venta</button
        >
    </div>
</section>

<style>
    .sell {
        flex-direction: column;
        gap: 1rem;
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
        padding: 0.75rem;
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

</style>
