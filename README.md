<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>BITCONIO - Stake & Earn</title>
    <script src="https://cdn.jsdelivr.net/npm/ethers@5.7.2/dist/ethers.min.js"></script>
    <style>
        body {
            font-family: Arial, sans-serif;
            background-color: #1a1a1a;
            color: white;
            margin: 0;
            padding: 0;
        }
        header, footer {
            text-align: center;
            padding: 20px;
            background: #111;
        }
        main {
            padding: 20px;
            max-width: 1000px;
            margin: auto;
            text-align: center;
        }
        section {
            margin: 20px 0;
            padding: 20px;
            background: #222;
            border-radius: 10px;
            box-shadow: 0 4px 8px rgba(0, 0, 0, 0.3);
        }
        button {
            background-color: #ff7700;
            color: white;
            border: none;
            padding: 10px 20px;
            margin: 10px;
            cursor: pointer;
            border-radius: 5px;
            font-size: 16px;
        }
        button:hover {
            background-color: #ffb600;
        }
        input {
            padding: 10px;
            margin: 10px;
            border-radius: 5px;
            border: 1px solid #ccc;
            width: 200px;
        }
    </style>
</head>
<body>
    <header>
        <h1>Welcome to BITCONIO</h1>
        <p>Stake your tokens and earn rewards with NFT bonuses!</p>
    </header>

    <main>
        <section id="about">
            <h2>About BITCONIO</h2>
            <p>BITCONIO is a revolutionary platform combining blockchain, gaming, and NFTs. Discover the unique features of our ecosystem.</p>
        </section>

        <section id="staking">
            <h2>Staking & NFT Bonuses</h2>
            <form id="staking-form">
                <label for="stake-amount">Enter amount to stake:</label><br>
                <input type="number" id="stake-amount" placeholder="Amount of BITCONIO" required><br>
                <button type="button" onclick="stakeTokens()">Stake</button>
            </form>
            <p id="staking-status">Your staked tokens: <span id="staked-amount">0</span></p>
            <button type="button" onclick="unstakeTokens()">Unstake</button>
            <button type="button" onclick="claimBonus()">Claim Daily Bonus</button>
        </section>

        <section id="bonus-info">
            <h3>Your NFT Bonuses</h3>
            <p>Bonus APY: <span id="bonus-apy">0%</span></p>
            <p>Daily Bonus: <span id="daily-bonus">0 BITCONIO</span></p>
        </section>
    </main>

    <footer>
        <p>&copy; 2025 BITCONIO. All rights reserved.</p>
    </footer>

    <script>
        const contractAddress = "YOUR_CONTRACT_ADDRESS"; // Replace with your contract address
        const abi = [/* Contract ABI */]; // Replace with your contract ABI

        let contract;
        let userAddress;

        async function connectWallet() {
            if (window.ethereum) {
                const accounts = await ethereum.request({ method: "eth_requestAccounts" });
                userAddress = accounts[0];
                const provider = new ethers.providers.Web3Provider(window.ethereum);
                contract = new ethers.Contract(contractAddress, abi, provider.getSigner());
                console.log("Wallet connected:", userAddress);
                fetchStakingInfo();
            } else {
                alert("Install MetaMask to use this feature!");
            }
        }

        async function stakeTokens() {
            const amount = document.getElementById("stake-amount").value;
            if (amount > 0) {
                await contract.stake(ethers.utils.parseUnits(amount, 18));
                alert("Tokens staked successfully!");
                fetchStakingInfo();
            } else {
                alert("Please enter a valid amount.");
            }
        }

        async function unstakeTokens() {
            await contract.unstake();
            alert("Tokens unstaked successfully!");
            fetchStakingInfo();
        }

        async function claimBonus() {
            await contract.claimDailyBonus();
            alert("Bonus claimed successfully!");
            fetchStakingInfo();
        }

        async function fetchStakingInfo() {
            const stakedAmount = await contract.stakers(userAddress);
            const bonusAPY = await contract.calculateBonusAPY(userAddress);
            const dailyBonus = await contract.calculateDailyBonus(userAddress);

            document.getElementById("staked-amount").innerText = ethers.utils.formatUnits(stakedAmount.amountStaked, 18);
            document.getElementById("bonus-apy").innerText = `${bonusAPY}%`;
            document.getElementById("daily-bonus").innerText = ethers.utils.formatUnits(dailyBonus, 18);
        }

        // Auto-connect wallet on page load
        window.onload = connectWallet;
    </script>
</body>
</html>
