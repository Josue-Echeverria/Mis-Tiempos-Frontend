<script lang="ts">
    import QrCode from 'svelte-qrcode';

    export let data: string;
    export let onClose: () => void;
</script>

<div class="modal-overlay" on:click={onClose}>
    <div class="modal-content" on:click|stopPropagation>
        <h2>Código QR de la Venta</h2>
        <div class="qr-container">
            <QrCode value={data} />
        </div>
        <button on:click={onClose}>Cerrar</button>
    </div>
</div>

<style>
    .modal-overlay {
        position: fixed;
        top: 0;
        left: 0;
        width: 100%;
        height: 100%;
        background-color: rgba(0, 0, 0, 0.5);
        display: flex;
        justify-content: center;
        align-items: center;
        z-index: 1000;
    }

    .modal-content {
        background-color: white;
        padding: 2rem;
        border-radius: 8px;
        text-align: center;
        color: black;
    }

    .qr-container {
        margin: 1rem 0;
    }
</style>
