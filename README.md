# Hatch

**We will release a youtube video soon walking you through the steps!**

### Requirements
- [Node >= 16](https://nodejs.org/en/)
- [Git](https://github.com/git-guides/install-git)
- We highly recommend using **Plug Wallet** for the funding stage, as they have WICP functionality built in to their wallet.

### Step 1 - Install hatch CLI and clone this repo
1. Install [hatch-cli](https://www.npmjs.com/package/hatch-cli) via npm. It will automatically handle the NFT backend for you. <br/> `sudo npm install -g hatch-cli`. You can also run `sudo npm update -g hatch-cli` if you already have installed and just need to update. We highly recommend you install it globally, but you can also install hatch-cli only in the local directory.
2. Navigate to a directory you want to work in and run:<br/>
`git clone https://github.com/kxl4126/hatch-cli-client.git`

### Step 2 - Set up pinata for IPFS
You will need to setup IPFS with [Pinata](https://app.pinata.cloud/). We use IPFS for now, because it essentially offers unbounded file storage for any file type. User experience on the IC will still be the same.
1. Create an API key. ![Create API](https://i.imgur.com/rGHvK5g.png)
2. Save your API keys. ![API Keys](https://i.imgur.com/jtMINuH.png)
Pinata currently only allows 1GB free storage. You may need to choose a paid plan ($0.15/GB) to upload all necessary asset files.
### Create basic directory structure
Make `keys/`, `assets/`, and `metadata/` folders. You will need these later.
The folder structure should look something like:
![Folder Structure](https://i.imgur.com/hib3a0g.png)

### Step 3 - Configure your identity and fund your minting account
1. Place your `Ed25519` key in `keys/keys.json`. If you don't have one, you can also allow the CLI to **generate a temporary identity** for you, by running: `hatch-cli identity`. **This identity is only used for minting and is different from your identity for your wallet or on the dfx CLI.**
2. Run `hatch-cli identity`. 
Save the output principal of the previous command. This is your minting account that needs to be funded for the CLI. Note that this account will be only used to manage your canister from the command line. Your NFTs and sale funds will still be minted to an account of your choice.
3. **We are currently in beta, so you must message us to add you as an authorized creator with this identity**.
4. Obtain **Psychedelic's WICP**. You need around ~ 2 WICP in the minting account. You can get WICP in the following ways:
   - Swap ICP for WICP with [Sonic](https://sonic.ooo/)
   - Or mint WICP directly from their canister. Follow the instructions at https://github.com/Psychedelic/wicp.
   - Or wrap your ICP with the wallet interface at [Hatch](https://hatch.market/).
5. **Transfer WICP** to your minting account principal. You can do this in the following ways:
   -  Use the Plug Wallet interface to transfer WICP to your minting principal.
   -  Or call transfer function directly from their canister to your minting principal. Follow the instructions at https://github.com/Psychedelic/wicp.

### Step 4 - Configure your collection
Check the `config/` directory to manage different configuration options for your collection.
1. **Configure your initial canister metadata.**
   1. Edit `config/config.json` to include basic metadata for your collection.
      - `name`: Your NFT collection's title (string)
      - `description`: Asentence about your NFT. This will generally accompany the title (string)
      - `supply`: how many NFTs your collection will hold (number)
      - `owner`: The `owner` field is the **principal id** that you NFTs will be minted to. It will also be the place where your **public sale funds are sent**, so make sure it is correct! (string)
      - `ownerWalletAddress`: This is your wallet address, which will be used to wrap/unwrap ICP using the cli, if you choose to do so.
      - `saleName`: The `saleName` field must be **unique**, as it identifies your project sale link. It can only contain alphanumeric characters, or one of "-" , "." , "_" , "~"  (string)
      - `contentType`: The content type of your NFTs. Check [MIME types](https://developer.mozilla.org/en-US/docs/Web/HTTP/Basics_of_HTTP/MIME_types/Common_types) for valid content types. Right now, we only support `image/png` and `image/gif`.
      - `pinataSecretKey`: The pinata secret key you saved.
      - `pinataApiKey`: The pinata api key you saved.
   
   2. Edit `config/links.json` with links to your media. This includes `gif`, `logo`, `banner`, `twitter`, etc.
2. **Place your NFT assets and metadata in the correct location.**
   1. Move your images (pngs) into the `assets/` directory. Make sure they are named properly --- `0.png`, `1.png`, ...  This serves as their NFT id. For now, we only support **png and gif files**.
   2. Move your metadata (json) into the `metadata/` directory. Make sure they are named properly --- `0.json`, `1.json`, ... This serves as the corresponding NFT metadata. **Right now, all metadata is restricted to text metadata**. We hope to make this more complex and specific in the future.
   **Example folder structure:**
   ![Assets](https://i.imgur.com/3Ny9myT.png)
   ![Metadata](https://i.imgur.com/ZsaiwQI.png)
    **Example metadata format**:
    ```
    {
        "background": "blue",
        "head": "large",
        "eyes": "round"
        ...
    }
    ```
    **Make sure your metadata matches up with your assets!**

### Step 5 - Initialize your NFT canister and mint your NFTs!
**DO NOT transfer your NFTs after minting them. This will mess up the configuration of the sale.** 
1. **Run** `hatch-cli init` to initialize your NFT canister. You must have funded your minting principal to perform this step. **Make sure your `config/config.json` is correct! You can only run this step once and all information is final!**
2. **Run** `hatch-cli validate` to do a basic check that all your files are valid.
3. **Run** `hatch-cli mint` to **mint your NFTs**! This process can take a while. If you do not care about the actual order your NFTs are minted in, you can mint in parallel with `hatch-cli mint -p`. The canister will save which NFTs you have minted, so feel free to pause the process at any time and re-run `hatch-cli mint` to continue where you left off! All NFTs will be minted to `owner` specified in config.
4. (Optional, Recommended) **Configure DAB** by submitting this [form](https://dab-ooo.typeform.com/nft-list?typeform-source=hatch.market). This will allow your NFTs to be surfaced on Plug wallet. Choose **DIP721 V2** and link this https://github.com/Psychedelic/DIP721 as the standard for the nft.

### Step 6 - Configure and host your public sale
**Here we will set up the properties of your public sale!**
1. **Edit config/saleConfig.json.**
   - `saleTime` represents the unix timestamp (in seconds) of your public sale start time.
   - `whitelistDiscount` represents how much discount (in ICP) whitelisted members get.
   - `reserveAmount` is how many NFTs to exclude from the sale. Those will be kept in the owner account.
   - `transactionLimit` is how many NFTs a user can be bought in one transaction.
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
    The example `prices` here means the following:
    - NFTs 0-1: 0.8 WICP
    - NFTs 2-3: 0.9 WICP
    - NFTs 4-5: 1 WICP
    - NFTs 6-7: 1.1 WICP
    - NFTs 8+: 1.2 WICP
   We recommend **just having one price**, which lets the sale runs smoother and quicker. Example: `[[0, 0.8]`.
2. (Optional) Add whitelisted principals to `config/whitelist.json`. These principals will be given the whitelist discount specified in `saleConfig.json`. This should take the array format:
```
["w4p43-4ps3u-broth-qnrd4-b7xku-brckl-fsnw6-wymoq-dle44-viw3y-bqe", ...]
```
3. **Run** `hatch-cli sale` to set up your sale. You can always change your sale configuration, as long as no NFTs have been sold yet. **Run `hatch-cli sale -f` to force another set up sale with a different `config/saleConfig.json`**.


**All that's left is to wait for the big day!**

View your sale at `https://hatch.market/sale/{saleName}`, where saleName is the `saleName` you specified in `config/config.json`.

### Step 7 - Claiming your funds
1. **Run** `hatch-cli claim` to claim your funds. **Your pending WICP will be deposited to your creator principal**.


### Coming Soon
**Post-launch management**
- Managing and distributing airdrops to supporters
- Listing on marketplace
- UI Dashboard for administration

**Broader NFT support**
- More complex and flexible metadata besides text.
- More customization options and more supported interfaces.
- Marketplace Support
### Contact  
If you need assistance in setting anything up, please contact us at:
- [Discord](https://discord.gg/n77xjyspDR)