<script>
    import coinsResults from "../utils/coinsResponse";
    let coins = [];
    let cloneCoins = [];
    let titles = ["#", "Coin", "Price", "Price change", "24 Volume"];
    const loadCoins = async (currency = "usd") => {
        try {
            const res = await fetch(
                `https://api.coingecko.com/api/v3/coins/markets?vs_currency=${currency}&order=market_cap_desc&per_page=100&page=1&sparkline=false&locale=en`
            );
            const data = await res.json();
            coins = data;
            cloneCoins = data;
            console.log("====================================");
            console.log(data);
            console.log("====================================");
        } catch (error) {
            console.log("====================================");
            console.log("Error al consultar la API");
            console.log("====================================");
            coins = coinsResults;
        }
    };
    const isNegative = (value) => {
        if (value < 0) return true;
        return false;
    };
    loadCoins("usd");
    let searchTerm = undefined;
    const searchCoin = (value) => {
        searchTerm = value;
        if (value.length > 0) {
            coins = coins.filter((coin) => {
                return (
                    coin.name.toLowerCase().includes(value.toLowerCase()) ||
                    coin.symbol.toLowerCase().includes(value.toLowerCase())
                );
            });
        }else{
            coins = cloneCoins;
        }
    };
</script>

<div class="container">
    <div class="row justify-content-center">
        <div class="col-md-12">
            <h1>Coin Market</h1>
            <input
                type="text"
                class="form-control bg-dark text-white rounded-0 border-0 my-4"
                placeholder="Ingresa tu moneda"
                on:keyup={({ target: { value } }) => searchCoin(value)}
            />
            {#if typeof searchTerm === "string" && searchTerm.length > 0}
                <p class="text-white">{searchTerm}</p>
            {/if}
            <table class="table table-striped table-hover table-dark">
                <thead>
                    <tr>
                        {#each titles as title}
                            <th>{title}</th>
                        {/each}
                    </tr>
                </thead>
                <tbody>
                    {#each coins as coin, i}
                        <tr>
                            <td class="text-muted">{i + 1}</td>
                            <td>
                                <img
                                    src={coin.image}
                                    class="thumbnail img-fluid me-3"
                                    alt={coin.name}
                                />
                                <span>{coin.name}</span>
                                <span class="text-uppercase text-muted"
                                    >{coin.symbol}</span
                                >
                            </td>
                            <td>{coin.current_price}</td>
                            <td
                                class={isNegative(
                                    coin.price_change_percentage_24h
                                )
                                    ? "text-success"
                                    : "text-danger"}
                            >
                                <span>
                                    {coin.price_change_percentage_24h.toFixed(
                                        2
                                    )}
                                </span>
                                <span>%</span>
                            </td>
                            <td>${coin.total_volume}</td>
                        </tr>
                    {/each}
                </tbody>
            </table>
        </div>
    </div>
</div>

<style>
    table {
        font-size: 0.85em;
    }
    .thumbnail {
        border-radius: 100%;
        width: fit-content;
        max-width: 22px;
    }
    .badge {
        background: rgb(66, 66, 66) !important;
    }
    .danger {
        color: rgb(250, 83, 83) !important;
    }
</style>
