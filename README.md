# Hatch

**We will release a youtube video soon walking you through the steps!**

### Requirements
- [Node](https://nodejs.org/en/)
- We recommend using **Plug Wallet** for the funding stage, as they have WICP built in to their wallet.
### Install hatch CLI
This is the tool that will automatically handle the NFT backend for you.
`npm install hatch-cli`

### Configure your identity and fund your minting account
1. Place your `Ed25519` key in `keys/keys.json`. You can also allow the CLI to **generate a temporary identity** for you, by running: `hatch-cli identity`
2. Run `hatch-cli identity`. 
Save the output principal of the previous command. This is your minting account that needs to be funded for the CLI. Note that this account will be only used to manage your canister from the command line. Your NFTs and sale funds will still be minted to an account of your choice.
3. Obtain **Psychedelic's WICP**. You need around ~ 1 WICP in the minting account. You can get WICP in the following ways:
   - Swap ICP for WICP with [Sonic](https://sonic.ooo/)
   - Or mint WICP directly from their canister. Follow the instructions at https://github.com/Psychedelic/wicp.
   - Or wrap your ICP with the wallet interface at [Hatch](hatch.com).
4. **Transfer WICP** to your minting account principal. You can do this in the following ways:
   -  Use the Plug Wallet interface to transfer WICP to your minting principal.
   -  Or call transfer function directly from their canister to your principal. Follow the instructions at https://github.com/Psychedelic/wicp.

### Configure your collection
Check the `config/` directory to manage different configuration options for your collection.
1. **Configure your initial canister metadata.**
a. Edit `config/config.json` to include basic metadata for your collection, like `name`, `supply`, and `description`. The `saleName` field must be **unique**, as it identifies your project sale link. The `owner` field is the **principal id** that you NFTs will be minted to. It will also be the place where your **public sale funds are sent**, so make sure it is correct!
b. Edit `config/links.json` with links to your media. This includes `gif`, `logo`, `banner`, `twitter`, etc.
2. **Place your NFT assets and metadata in the correct location.**
   - Move your images (pngs) into the `assets/` directory. Make sure they are named properly --- `0.png`, `1.png`, ...  This serves as their NFT id.
   - Move your metadata (json) into the `assets/` directory. Make sure they are named properly --- `0.json`, `1.json`, ... This serves as the corresponding NFT metadata.  
   **Example folder structure:**
   ![Assets](https://i.imgur.com/3Ny9myT.png)
   ![Metadata](https://i.imgur.com/ZsaiwQI.png)
    **Example metadata format**:
    ```
    {
        "background": blue,
        "head": large,
        "eyes": round
        ...
    }
    ```
    **Make sure your metadata matches up with your assets!**

### Initialize your NFT canister and mint your NFTs!
1. **Run** `hatch-cli init` to initialize your NFT canister. You must have funded your minting principal to perform this step. **Make sure your `config/config.json` is correct! You can only run this step once and all information is final!**
2. **Run** `hatch-cli validate` to do a basic check that all your files are valid.
3. **Run** `hatch-cli mint` to **mint your NFTs**! This process can take a while. The canister will save which NFTs you have minted, so feel free to pause the process at any time and re-run `hatch-cli mint` to continue where you left off! All NFTs will be minted to `owner` specified in config.


### Configure and host your public sale
**Here we will set up the properties of your public sale!**
1. **Edit config/saleConfig.json.**
   - `saleTime` represents the unix timestamp (in seconds) of your public sale start time.
   - `whitelistDiscount` represents how much discount (in ICP) whitelisted members get.
   - `reserveAmount` is how many NFTs to exclude from the sale. Those will be kept in the owner account.
   - `prices` represents the pricing configuration desired. It takes the format of an array of tuples [nft_id, price], where `nft_id` represents the starting nft id for a particular price tier, and price represents the price (in WICP) of that tier. Example shown below.
   **Example saleConfig.json**
   ```
   {
        "saleTime": 164810226,
        "prices": [[0, 0.8], [2, 0.9], [4, 1], [6, 1.1], [8, 1.2]],
        "whitelistDiscount": 0.1,
        "reserveAmount": 0
    }
    ```
    The `prices` here means the following:
    - NFTs 0-1: 0.8 WICP
    - NFTs 2-3: 0.9 WICP
    - NFTs 4-5: 1 WICP
    - NFTs 6-7: 1.1 WICP
    - NFTs 8+: 1.2 WICP
2. (Optional) Add whitelisted principals to `config/whitelist.json`. These principals will be given the whitelist discount specified in `saleConfig.json`. This should take the array format:
```
["w4p43-4ps3u-broth-qnrd4-b7xku-brckl-fsnw6-wymoq-dle44-viw3y-bqe", ...]
```
3. **Run** `hatch-cli sale` to set up your sale. You can always change your sale configuration, as long as no NFTs have been sold yet. **Run `hatch-cli sale -f` to force another set up sale with a different `config/saleConfig.json`**.
