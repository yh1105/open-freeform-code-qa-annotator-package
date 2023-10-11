
# Post \#70224185 [Link](https://stackoverflow.com/questions/70224185/)

## How to transfer custom SPL token by '@solana/web3.js' and '@solana/sol-wallet-adapter'

**Vote**: 19 (269/702) **Views**: 16347 (339/702) 

**Internal ID** \#0-0-51

Created at 2021-12-04 08:41:35

Tags: `javascript` `cryptography` `token` `solana`

----------

#### Metadata:

Area: `Front-end`

Language: `javascript` (full parsed tag list: `javascript`)

----------

**Notepad**


----------

I am trying to transfer a custom SPL token with the solana-wallet adapter. However I am having trouble getting the wallet's secret key/signing the transaction.
I've looked at these answers for writing the transfer code, but I need to get the Signer and I have trouble figuring out how with solana-wallet adapter:
[How can you transfer SOL using the web3.js sdk for Solana?](https://stackoverflow.com/questions/68166964/how-can-you-transfer-sol-using-the-web3-js-sdk-for-solana)
[How to transfer custom token by '@solana/web3.js'](https://stackoverflow.com/questions/68236211/how-to-transfer-custom-token-by-solana-web3-js)
These examples hardcode the secret key and since I'm using a wallet extension this is not possible.
According to this issue on the webadapter repo [https://github.com/solana-labs/wallet-adapter/issues/120](https://github.com/solana-labs/wallet-adapter/issues/120) you need to:

1. Create a @solana/web3.js Transaction object and add instructions to it
2. Sign the transaction with the wallet
3. Send the transaction over a Connection


But Ii am having difficulty finding examples or documentation as to how to do step 1 and 2.
```
const SendTransaction: React.FC<Props> = ({ children }) => {
    const { connection } = useConnection()
    const { publicKey, sendTransaction } = useWallet()

    const onSendSPLTransaction = useCallback(
        async (toPubkey: string, amount: number) => {
            if (!toPubkey || !amount) return
            const toastId = toast.loading('Processing transaction...')

            try {
                if (!publicKey) throw new WalletNotConnectedError()
                const toPublicKey = new PublicKey(toPubkey)
                const mint = new PublicKey('Mint address')
                const payer = '????' // how to get this Signer
                const token = new Token(connection, mint, TOKEN_PROGRAM_ID, payer)
                const fromTokenAccount = await token.getOrCreateAssociatedAccountInfo(publicKey)
                const toTokenAccount = await token.getOrCreateAssociatedAccountInfo(toPublicKey)

                const transaction = new Transaction().add(
                    Token.createTransferInstruction(
                        TOKEN_PROGRAM_ID,
                        fromTokenAccount.address,
                        toTokenAccount.address,
                        publicKey,
                        [],
                        0
                    )
                )

                const signature = await sendTransaction(transaction, connection)

                const response = await connection.confirmTransaction(signature, 'processed')
                console.log('response', response)
                toast.success('Transaction sent', {
                    id: toastId,
                })
            } catch (error) {
                toast.error(`Transaction failed: ${error.message}`, {
                    id: toastId,
                })
            }
        },
        [publicKey, sendTransaction, connection]
    )

    return <>{children(onSendSPLTransaction)}</>
}
```



----------
        
## Answer \#0

**Accepted** Vote: 12

Created at 2021-12-06 13:51:48

------------

So i found a way to do this, it requires some cleanup and error handling but allows for a custom token transaction via `@solana/wallet-adapter`.
```
// sendTransaction.tsx
import { WalletNotConnectedError } from '@solana/wallet-adapter-base'
import { useConnection, useWallet } from '@solana/wallet-adapter-react'
import { Transaction, PublicKey, LAMPORTS_PER_SOL } from '@solana/web3.js'
import React, { useCallback } from 'react'
import { toast } from 'react-hot-toast'
import { TOKEN_PROGRAM_ID } from '@solana/spl-token'
import { getOrCreateAssociatedTokenAccount } from './getOrCreateAssociatedTokenAccount'
import { createTransferInstruction } from './createTransferInstructions'

interface Props {
    children: (sendTransaction: OnSendTransaction) => React.ReactNode
}

type OnSendTransaction = (toPublicKey: string, amount: number) => void

// Docs: https://github.com/solana-labs/solana-program-library/pull/2539/files
// https://github.com/solana-labs/wallet-adapter/issues/189
// repo: https://github.com/solana-labs/example-token/blob/v1.1/src/client/token.js
// creating a token for testing: https://learn.figment.io/tutorials/sol-mint-token
const SendTransaction: React.FC<Props> = ({ children }) => {
    const { connection } = useConnection()
    const { publicKey, signTransaction, sendTransaction } = useWallet()

    const onSendSPLTransaction = useCallback(
        async (toPubkey: string, amount: number) => {
            if (!toPubkey || !amount) return
            const toastId = toast.loading('Processing transaction...')

            try {
                if (!publicKey || !signTransaction) throw new WalletNotConnectedError()
                const toPublicKey = new PublicKey(toPubkey)
                const mint = new PublicKey('MINT ADDRESS')

                const fromTokenAccount = await getOrCreateAssociatedTokenAccount(
                    connection,
                    publicKey,
                    mint,
                    publicKey,
                    signTransaction
                )

                const toTokenAccount = await getOrCreateAssociatedTokenAccount(
                    connection,
                    publicKey,
                    mint,
                    toPublicKey,
                    signTransaction
                )

                const transaction = new Transaction().add(
                    createTransferInstruction(
                        fromTokenAccount.address, // source
                        toTokenAccount.address, // dest
                        publicKey,
                        amount * LAMPORTS_PER_SOL,
                        [],
                        TOKEN_PROGRAM_ID
                    )
                )

                const blockHash = await connection.getRecentBlockhash()
                transaction.feePayer = await publicKey
                transaction.recentBlockhash = await blockHash.blockhash
                const signed = await signTransaction(transaction)

                await connection.sendRawTransaction(signed.serialize())

                toast.success('Transaction sent', {
                    id: toastId,
                })
                // eslint-disable-next-line @typescript-eslint/no-explicit-any
            } catch (error: any) {
                toast.error(`Transaction failed: ${error.message}`, {
                    id: toastId,
                })
            }
        },
        [publicKey, sendTransaction, connection]
    )

    return <>{children(onSendSPLTransaction)}</>
}

export default SendTransaction
```

```
// getOrCreateAssociatedTokenAccount.ts
import { TOKEN_PROGRAM_ID, ASSOCIATED_TOKEN_PROGRAM_ID } from '@solana/spl-token'
import { SignerWalletAdapterProps } from '@solana/wallet-adapter-base'
import { Connection, PublicKey, Commitment, Transaction } from '@solana/web3.js'
import { createAssociatedTokenAccountInstruction } from './createAssociatedTokenAccountInstruction'
import { getAccountInfo } from './getAccountInfo'
import { getAssociatedTokenAddress } from './getAssociatedTokerAddress'

export async function getOrCreateAssociatedTokenAccount(
    connection: Connection,
    payer: PublicKey,
    mint: PublicKey,
    owner: PublicKey,
    signTransaction: SignerWalletAdapterProps['signTransaction'],
    allowOwnerOffCurve = false,
    commitment?: Commitment,
    programId = TOKEN_PROGRAM_ID,
    associatedTokenProgramId = ASSOCIATED_TOKEN_PROGRAM_ID
) {
    const associatedToken = await getAssociatedTokenAddress(
        mint,
        owner,
        allowOwnerOffCurve,
        programId,
        associatedTokenProgramId
    )

    // This is the optimal logic, considering TX fee, client-side computation, RPC roundtrips and guaranteed idempotent.
    // Sadly we can't do this atomically.
    let account
    try {
        account = await getAccountInfo(connection, associatedToken, commitment, programId)
        // eslint-disable-next-line @typescript-eslint/no-explicit-any
    } catch (error: any) {
        // TokenAccountNotFoundError can be possible if the associated address has already received some lamports,
        // becoming a system account. Assuming program derived addressing is safe, this is the only case for the
        // TokenInvalidAccountOwnerError in this code path.
        if (error.message === 'TokenAccountNotFoundError' || error.message === 'TokenInvalidAccountOwnerError') {
            // As this isn't atomic, it's possible others can create associated accounts meanwhile.
            try {
                const transaction = new Transaction().add(
                    createAssociatedTokenAccountInstruction(
                        payer,
                        associatedToken,
                        owner,
                        mint,
                        programId,
                        associatedTokenProgramId
                    )
                )

                const blockHash = await connection.getRecentBlockhash()
                transaction.feePayer = await payer
                transaction.recentBlockhash = await blockHash.blockhash
                const signed = await signTransaction(transaction)

                const signature = await connection.sendRawTransaction(signed.serialize())

                await connection.confirmTransaction(signature)
            } catch (error: unknown) {
                // Ignore all errors; for now there is no API-compatible way to selectively ignore the expected
                // instruction error if the associated account exists already.
            }

            // Now this should always succeed
            account = await getAccountInfo(connection, associatedToken, commitment, programId)
        } else {
            throw error
        }
    }

    if (!account.mint.equals(mint.toBuffer())) throw Error('TokenInvalidMintError')
    if (!account.owner.equals(owner.toBuffer())) throw new Error('TokenInvalidOwnerError')

    return account
}
```

```
// createAssociatedTokenAccountInstruction.ts
import { TOKEN_PROGRAM_ID, ASSOCIATED_TOKEN_PROGRAM_ID } from '@solana/spl-token'
import { PublicKey, TransactionInstruction, SystemProgram, SYSVAR_RENT_PUBKEY } from '@solana/web3.js'

export function createAssociatedTokenAccountInstruction(
    payer: PublicKey,
    associatedToken: PublicKey,
    owner: PublicKey,
    mint: PublicKey,
    programId = TOKEN_PROGRAM_ID,
    associatedTokenProgramId = ASSOCIATED_TOKEN_PROGRAM_ID
): TransactionInstruction {
    const keys = [
        { pubkey: payer, isSigner: true, isWritable: true },
        { pubkey: associatedToken, isSigner: false, isWritable: true },
        { pubkey: owner, isSigner: false, isWritable: false },
        { pubkey: mint, isSigner: false, isWritable: false },
        { pubkey: SystemProgram.programId, isSigner: false, isWritable: false },
        { pubkey: programId, isSigner: false, isWritable: false },
        { pubkey: SYSVAR_RENT_PUBKEY, isSigner: false, isWritable: false },
    ]

    return new TransactionInstruction({
        keys,
        programId: associatedTokenProgramId,
        data: Buffer.alloc(0),
    })
}
```

```
// createTransferInstructions.ts
import { TOKEN_PROGRAM_ID } from '@solana/spl-token'
import { AccountMeta, PublicKey, Signer, TransactionInstruction } from '@solana/web3.js'
import BufferLayout from 'buffer-layout'
import BN from 'bn.js'

export enum TokenInstruction {
    InitializeMint = 0,
    InitializeAccount = 1,
    InitializeMultisig = 2,
    Transfer = 3,
    Approve = 4,
    Revoke = 5,
    SetAuthority = 6,
    MintTo = 7,
    Burn = 8,
    CloseAccount = 9,
    FreezeAccount = 10,
    ThawAccount = 11,
    TransferChecked = 12,
    ApproveChecked = 13,
    MintToChecked = 14,
    BurnChecked = 15,
    InitializeAccount2 = 16,
    SyncNative = 17,
    InitializeAccount3 = 18,
    InitializeMultisig2 = 19,
    InitializeMint2 = 20,
}

/**
 * Construct a Transfer instruction
 *
 * @param source       Source account
 * @param destination  Destination account
 * @param owner        Owner of the source account
 * @param amount       Number of tokens to transfer
 * @param multiSigners Signing accounts if `owner` is a multisig
 * @param programId    SPL Token program account
 *
 * @return Instruction to add to a transaction
 */
export function createTransferInstruction(
    source: PublicKey,
    destination: PublicKey,
    owner: PublicKey,
    amount: number,
    multiSigners: Signer[] = [],
    programId = TOKEN_PROGRAM_ID
): TransactionInstruction {
    const dataLayout = BufferLayout.struct([BufferLayout.u8('instruction'), BufferLayout.blob(8, 'amount')])

    const keys = addSigners(
        [
            { pubkey: source, isSigner: false, isWritable: true },
            { pubkey: destination, isSigner: false, isWritable: true },
        ],
        owner,
        multiSigners
    )

    const data = Buffer.alloc(dataLayout.span)
    dataLayout.encode(
        {
            instruction: TokenInstruction.Transfer,
            amount: new TokenAmount(amount).toBuffer(),
        },
        data
    )

    return new TransactionInstruction({ keys, programId, data })
}

export function addSigners(keys: AccountMeta[], ownerOrAuthority: PublicKey, multiSigners: Signer[]): AccountMeta[] {
    if (multiSigners.length) {
        keys.push({ pubkey: ownerOrAuthority, isSigner: false, isWritable: false })
        for (const signer of multiSigners) {
            keys.push({ pubkey: signer.publicKey, isSigner: true, isWritable: false })
        }
    } else {
        keys.push({ pubkey: ownerOrAuthority, isSigner: true, isWritable: false })
    }
    return keys
}

class TokenAmount extends BN {
    /**
     * Convert to Buffer representation
     */
    toBuffer(): Buffer {
        const a = super.toArray().reverse()
        const b = Buffer.from(a)
        if (b.length === 8) {
            return b
        }

        if (b.length >= 8) {
            throw new Error('TokenAmount too large')
        }

        const zeroPad = Buffer.alloc(8)
        b.copy(zeroPad)
        return zeroPad
    }

    /**
     * Construct a TokenAmount from Buffer representation
     */
    static fromBuffer(buffer: Buffer): TokenAmount {
        if (buffer.length !== 8) {
            throw new Error(`Invalid buffer length: ${buffer.length}`)
        }

        return new BN(
            [...buffer]
                .reverse()
                .map((i) => `00${i.toString(16)}`.slice(-2))
                .join(''),
            16
        )
    }
}
```

```
// getAccountInfo.ts
import { TOKEN_PROGRAM_ID, AccountLayout } from '@solana/spl-token'
import { Connection, PublicKey, Commitment } from '@solana/web3.js'

export enum AccountState {
    Uninitialized = 0,
    Initialized = 1,
    Frozen = 2,
}

export async function getAccountInfo(
    connection: Connection,
    address: PublicKey,
    commitment?: Commitment,
    programId = TOKEN_PROGRAM_ID
) {
    const info = await connection.getAccountInfo(address, commitment)
    if (!info) throw new Error('TokenAccountNotFoundError')
    if (!info.owner.equals(programId)) throw new Error('TokenInvalidAccountOwnerError')
    if (info.data.length != AccountLayout.span) throw new Error('TokenInvalidAccountSizeError')

    const rawAccount = AccountLayout.decode(Buffer.from(info.data))

    return {
        address,
        mint: rawAccount.mint,
        owner: rawAccount.owner,
        amount: rawAccount.amount,
        delegate: rawAccount.delegateOption ? rawAccount.delegate : null,
        delegatedAmount: rawAccount.delegatedAmount,
        isInitialized: rawAccount.state !== AccountState.Uninitialized,
        isFrozen: rawAccount.state === AccountState.Frozen,
        isNative: !!rawAccount.isNativeOption,
        rentExemptReserve: rawAccount.isNativeOption ? rawAccount.isNative : null,
        closeAuthority: rawAccount.closeAuthorityOption ? rawAccount.closeAuthority : null,
    }
}
```

```
// getAssociatedTokerAddress.ts
import { TOKEN_PROGRAM_ID, ASSOCIATED_TOKEN_PROGRAM_ID } from '@solana/spl-token'
import { PublicKey } from '@solana/web3.js'

export async function getAssociatedTokenAddress(
    mint: PublicKey,
    owner: PublicKey,
    allowOwnerOffCurve = false,
    programId = TOKEN_PROGRAM_ID,
    associatedTokenProgramId = ASSOCIATED_TOKEN_PROGRAM_ID
): Promise<PublicKey> {
    if (!allowOwnerOffCurve && !PublicKey.isOnCurve(owner.toBuffer())) throw new Error('TokenOwnerOffCurveError')

    const [address] = await PublicKey.findProgramAddress(
        [owner.toBuffer(), programId.toBuffer(), mint.toBuffer()],
        associatedTokenProgramId
    )

    return address
}
```

Hope this helps others. If any one has any remarks pointers please comment.


------------
    
    
## Answer \#1

 Vote: 4

Created at 2022-01-29 21:02:39

------------

Here is an example of generating keys, creating a token, creating token account for minter, minting tokens into token account and finally transfering tokens to another token account
```
const splToken = require("@solana/spl-token");
const web3 = require("@solana/web3.js");

const test = async () => {
  console.log("solana test");

  const cluster_url = "https://api.testnet.solana.com";

  //create keys
  const mint_authority = web3.Keypair.generate();
  const freeze_authority = web3.Keypair.generate();

  const connection = new web3.Connection(cluster_url, "confirmed");

  //airdrop to mint authority
  const airdropSignature = await connection.requestAirdrop(
    mint_authority.publicKey,
    web3.LAMPORTS_PER_SOL
  );

  await connection.confirmTransaction(airdropSignature);

  console.log("mint authority pub key", mint_authority.publicKey.toString());

  //check balance of mint authority
  console.log(
    "SOL balance of minter",
    await connection.getBalance(mint_authority.publicKey)
  );

  //create (but not mint) a new token
  const token = await splToken.Token.createMint(
    connection,
    mint_authority,
    mint_authority.publicKey,
    null,
    9,
    splToken.TOKEN_PROGRAM_ID
  );

  console.log(
    "new SOL balance of minter after token creation activity",
    await connection.getBalance(mint_authority.publicKey)
  );

  console.log("new spl token address is", token.publicKey.toString());

  //lets mint some of the token INTO the mint authority wallet
  const minter_token_account = await token.getOrCreateAssociatedAccountInfo(
    mint_authority.publicKey
  );
  console.log(
    "minter_token_account address",
    minter_token_account.address.toString()
  );
  console.log("minting some supply into token account we made for the minter");
  console.log(
    "minter_token_account.mint (same as token address)",
    minter_token_account.mint.toString()
  );
  console.log(
    "minter_token_account.owner (same as pub key of token account)",
    minter_token_account.owner.toString()
  );

  // Mint the tokens - how am I allowed to do this just my using mint_authority.publicKey as the authority, shouldnt I need
  // a private key to do this or is it just because I have a 'powerful' reference to the token because of earlier createMint() ????
  await token.mintTo(
    minter_token_account.address,
    mint_authority.publicKey,
    [],
    10000000
  );

  //get balance of sol for minter
  console.log(
    "new SOL balance of minter after token minting activity",
    await connection.getBalance(mint_authority.publicKey)
  );

  //token accounts by owner
  const tokenAccountsByOwner = await connection.getTokenAccountsByOwner(
    mint_authority.publicKey,
    {
      mint: token.publicKey,
    }
  );

  console.log(
    "tokenAccountsByOwner - token account address",
    tokenAccountsByOwner.value[0].pubkey.toString()
  );
  //   console.log(
  //     "tokenAccountsByOwner - token account lamports",
  //     tokenAccountsByOwner.value[0].account.lamports
  //   );

  //get token account balance
  const tokenAccountBalance = await connection.getTokenAccountBalance(
    minter_token_account.address
  );
  console.log(
    "token account balance:",
    tokenAccountBalance.value.uiAmountString
  );

  //now lets create a new solana address
  const bob = web3.Keypair.generate();

  console.log("bob public address:", bob.publicKey.toString());

  console.log(
    "SOL balance of minter just before creating account for bob",
    await connection.getBalance(mint_authority.publicKey)
  );

  //create token account fot this address
  const bob_token_account = await token.getOrCreateAssociatedAccountInfo(
    bob.publicKey
  );

  console.log(
    "SOL balance of minter just after creating account for bob",
    await connection.getBalance(mint_authority.publicKey)
  ); //seems to cost 2044280 lamports .002 SOL

  console.log(
    "bob_token_account address",
    bob_token_account.address.toString()
  );

  console.log(
    "bob_token_account.mint (same as token address)",
    bob_token_account.mint.toString()
  );
  console.log(
    "bob_token_account.owner (same as pub key of token account)",
    bob_token_account.owner.toString()
  );

  //transfer from minter wallet to bob
  var transferTrx = new web3.Transaction().add(
    splToken.Token.createTransferInstruction(
      splToken.TOKEN_PROGRAM_ID,
      minter_token_account.address,
      bob_token_account.address,
      mint_authority.publicKey,
      [],
      1
    )
  );

  let bobTokenAccountBalance = await connection.getTokenAccountBalance(
    bob_token_account.address
  );
  console.log(
    "bob_token_account balance before transfer:",
    bobTokenAccountBalance.value.uiAmountString
  );

  console.log(
    "SOL balance of minter just before transfering to bob",
    await connection.getBalance(mint_authority.publicKey)
  );

  var signature = await web3.sendAndConfirmTransaction(
    connection,
    transferTrx,
    [mint_authority]
  );

  console.log(
    "SOL balance of minter just AFTER transfering to bob",
    await connection.getBalance(mint_authority.publicKey)
  ); // seems to cost 5000 lamports or .000005 SOL

  bobTokenAccountBalance = await connection.getTokenAccountBalance(
    bob_token_account.address
  );
  console.log(
    "bob_token_account balance after transfer:",
    bobTokenAccountBalance.value.uiAmountString
  );
};

return test()
  .then()
  .catch((err) => {
    if (err.logs) {
      console.log(err.logs);
    } else {
      console.error(err.message);
    }
  });
```



------------
    
    
## Answer \#2

 Vote: 3

Created at 2022-10-04 04:29:36

------------

There are two problems in above answer

1. first we need to write lot of custom code when we can use built in function of '@solana/wallet-adapter', '@solana/spl-token' and '@solana/web3.js'.
2. Second if receiver account does not exist user will be charged two time by any connected wallet. First time it will charge to create account and second time it will charge to transfer spl token. Also it opens wallet approval modal two times which will again a bad thing for user.


Below code transfer 1 USDC(USDC is one of spl token on solana blockchain).
```
import React from 'react';
import {
  WalletNotConnectedError,
  SignerWalletAdapterProps
} from '@solana/wallet-adapter-base';
import { useConnection, useWallet } from '@solana/wallet-adapter-react';
import {
  createTransferInstruction,
  createAssociatedTokenAccountInstruction,
  getAssociatedTokenAddress,
  getAccount
} from '@solana/spl-token';
import {
  PublicKey,
  Transaction,
  Connection,
  TransactionInstruction
} from '@solana/web3.js';

export const configureAndSendCurrentTransaction = async (
  transaction: Transaction,
  connection: Connection,
  feePayer: PublicKey,
  signTransaction: SignerWalletAdapterProps['signTransaction']
) => {
  const blockHash = await connection.getLatestBlockhash();
  transaction.feePayer = feePayer;
  transaction.recentBlockhash = blockHash.blockhash;
  const signed = await signTransaction(transaction);
  const signature = await connection.sendRawTransaction(signed.serialize());
  await connection.confirmTransaction({
    blockhash: blockHash.blockhash,
    lastValidBlockHeight: blockHash.lastValidBlockHeight,
    signature
  });
  return signature;
};

const SendSolanaSplTokens: React.FC = () => {
  const { connection } = useConnection();
  const { publicKey, signTransaction } = useWallet();

  const handlePayment = async () => {
    try {
      if (!publicKey || !signTransaction) {
        throw new WalletNotConnectedError();
      }
      const mintToken = new PublicKey(
        '4zMMC9srt5Ri5X14GAgXhaHii3GnPAEERYPJgZJDncDU'
      ); // 4zMMC9srt5Ri5X14GAgXhaHii3GnPAEERYPJgZJDncDU is USDC token address on solana devnet
      const recipientAddress = new PublicKey(
        'token receiver solana account address'
      );

      const transactionInstructions: TransactionInstruction[] = [];
      const associatedTokenFrom = await getAssociatedTokenAddress(
        mintToken,
        publicKey
      );
      const fromAccount = await getAccount(connection, associatedTokenFrom);
      const associatedTokenTo = await getAssociatedTokenAddress(
        mintToken,
        recipientAddress
      );
      if (!(await connection.getAccountInfo(associatedTokenTo))) {
        transactionInstructions.push(
          createAssociatedTokenAccountInstruction(
            publicKey,
            associatedTokenTo,
            recipientAddress,
            mintToken
          )
        );
      }
      transactionInstructions.push(
        createTransferInstruction(
          fromAccount.address, // source
          associatedTokenTo, // dest
          publicKey,
          1000000 // transfer 1 USDC, USDC on solana devnet has 6 decimal
        )
      );
      const transaction = new Transaction().add(...transactionInstructions);
      const signature = await configureAndSendCurrentTransaction(
        transaction,
        connection,
        publicKey,
        signTransaction
      );
      // signature is transaction address, you can confirm your transaction on 'https://explorer.solana.com/?cluster=devnet'
    } catch (error) {}
  };

  return <button onClick={handlePayment}>Transfer spl token</button>;
};

export default SendSolanaSplTokens;
```



------------
    
    
## Answer \#3

 Vote: 2

Created at 2021-12-17 22:22:35

------------

It is a variant from your code that works and doesnt need to create our own transfer instruction. It only needs getOrCreateAssociatedTokenAccount, createAssociatedTokenAccountInstruction, getAccountInfo and getAssociatedTokenAddress (these functions are specified in the other answer).
You need to import spl-token to create transfer instruction.
```
const fromTokenAccount = await getOrCreateAssociatedTokenAccount(
  connection,
  publicKey,
  mint,
  publicKey,
  signTransaction
)

const toTokenAccount = await getOrCreateAssociatedTokenAccount(
    connection,
    publicKey,
    mint,
    toPublicKey,
    signTransaction
)

const transaction = new web3.Transaction().add(
  Token.createTransferInstruction(
      TOKEN_PROGRAM_ID,
      fromTokenAccount.address,
      toTokenAccount.address,
      publicKey,
      [],
      1
  )
)

const signature = await sendTransaction(transaction, connection)

const response = await connection.confirmTransaction(signature, 'processed')
console.log('response', response)
```



------------
    
    
## Answer \#4

 Vote: 0

Created at 2022-05-09 10:21:32

------------

can also be:

```
import {
  ASSOCIATED_TOKEN_PROGRAM_ID,
  TOKEN_PROGRAM_ID,
  getAssociatedTokenAddress,
  getAccount,
  createAssociatedTokenAccountInstruction,
  TokenAccountNotFoundError,
  TokenInvalidAccountOwnerError,
  TokenInvalidMintError,
  TokenInvalidOwnerError,
} from '@solana/spl-token'
import { Transaction } from '@solana/web3.js'

/**
 * (rewrite)Retrieve the associated token account, or create it if it doesn't exist
 *
 * @param connection               Connection to use
 * @param payer                    Payer of the transaction and initialization fees
 * @param mint                     Mint associated with the account to set or verify
 * @param owner                    Owner of the account to set or verify
 * @param sendTransaction
 * @param allowOwnerOffCurve       Allow the owner account to be a PDA (Program Derived Address)
 * @param commitment               Desired level of commitment for querying the state
 * @param programId                SPL Token program account
 * @param associatedTokenProgramId SPL Associated Token program account
 *
 * @return Address of the new associated token account
 */
export async function getOrCreateAssociatedTokenAccount(
  connection,
  payer,
  mint,
  owner,
  sendTransaction,
  allowOwnerOffCurve = false,
  commitment,
  programId = TOKEN_PROGRAM_ID,
  associatedTokenProgramId = ASSOCIATED_TOKEN_PROGRAM_ID,
) {
  const associatedToken = await getAssociatedTokenAddress(
    mint,
    owner,
    allowOwnerOffCurve,
    programId,
    associatedTokenProgramId,
  )
  // This is the optimal logic, considering TX fee, client-side computation, RPC roundtrips and guaranteed idempotent.
  // Sadly we can't do this atomically.
  let account
  try {
    account = await getAccount(connection, associatedToken, commitment, programId)
  } catch (error) {
    // TokenAccountNotFoundError can be possible if the associated address has already received some lamports,
    // becoming a system account. Assuming program derived addressing is safe, this is the only case for the
    // TokenInvalidAccountOwnerError in this code path.
    if (error instanceof TokenAccountNotFoundError || error instanceof TokenInvalidAccountOwnerError) {
      // As this isn't atomic, it's possible others can create associated accounts meanwhile.
      try {
        const transaction = new Transaction().add(
          createAssociatedTokenAccountInstruction(
            payer,
            associatedToken,
            owner,
            mint,
            programId,
            associatedTokenProgramId,
          ),
        )
        const signature = await sendTransaction(transaction, connection)

        await connection.confirmTransaction(signature)
      } catch (error) {
        // Ignore all errors; for now there is no API-compatible way to selectively ignore the expected
        // instruction error if the associated account exists already.
      }
      // Now this should always succeed
      account = await getAccount(connection, associatedToken, commitment, programId)
    } else {
      throw error
    }
  }
  if (!account.mint.equals(mint)) throw new TokenInvalidMintError()
  if (!account.owner.equals(owner)) throw new TokenInvalidOwnerError()
  return account
}
```


```
import { Transaction, PublicKey } from '@solana/web3.js'
import { createTransferCheckedInstruction } from '@solana/spl-token'
import { useWallet, useConnection } from '@solana/wallet-adapter-react'

try {
    const { sendTransaction, publicKey } = useWallet()
    const { connection } = useConnection()

    // step 1: create transaction
    const toPublicKey = new PublicKey('')
    const mint = new PublicKey('token address')
    const transaction = new Transaction()
    // up rewrite function
    const fromTokenAccount = await getOrCreateAssociatedTokenAccount(
        connection,
        publicKey,
        mint,
        publicKey,
        sendTransaction,
    )
    const toTokenAccount = await getOrCreateAssociatedTokenAccount(
        connection,
        publicKey,
        mint,
        toPublicKey,
        sendTransaction,
    )
    const instruction = createTransferCheckedInstruction(
        fromTokenAccount.address,
        mint,
        toTokenAccount.address,
        publicKey,
        1,
        0,
    )
    transaction.add(instruction)

    // step 2: sign&send transaction
    const result = await sendTransaction(transaction, connection)
} catch (err) {
    // err handle
}
```



------------
    
    
## Answer \#5

 Vote: 0

Created at 2022-08-01 21:17:57

------------


> I posted the question on the GitHub Repository of sol-wallet-adapter
and got this response:
[https://github.com/solana-labs/wallet-adapter/issues/189](https://github.com/solana-labs/wallet-adapter/issues/189)> Ah, this isn't really an issue with wallet-adapter. It's a shortcoming of the `Token` class -- it doesn't really work with a
wallet where you don't have the Keypair/Signer to pass around.There is a TypeScript port of `@spl-token` which makes some of this easier, but it's still under review and hasn't been released on NPM
yet:
[solana-labs/solana-program-library#2539](https://github.com/solana-labs/solana-program-library/pull/2539)I would recommend checking for the associated token account, and creating it yourself in an instruction to the transaction you sign
with the wallet. These links from that open PR may help in
understanding how to do this:[https://github.com/solana-labs/solana-program-library/blob/jsx/token-ts/token/ts/src/actions/getOrCreateAssociatedTokenAccount.ts](https://github.com/solana-labs/solana-program-library/blob/jsx/token-ts/token/ts/src/actions/getOrCreateAssociatedTokenAccount.ts)[https://github.com/solana-labs/solana-program-library/blob/jsx/token-ts/token/ts/src/state/mint.ts#L114-L129](https://github.com/solana-labs/solana-program-library/blob/jsx/token-ts/token/ts/src/state/mint.ts#L114-L129)[https://github.com/solana-labs/solana-program-library/blob/jsx/token-ts/token/ts/src/instructions/transfer.ts](https://github.com/solana-labs/solana-program-library/blob/jsx/token-ts/token/ts/src/instructions/transfer.ts)I'm closing this since it's out of scope for this library, but I hope it helps.


------------
    
    