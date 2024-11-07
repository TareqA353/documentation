# useSessionWithDistributedKey

## Description

Uses a previously created session ([see here](/smartAccountsV2/react/useCreateSessionWithDistributedKey)) which sends transactions in the context of a users smart account.

## Usage

```tsx
import { useSessionWithDistributedKey, useUserOpWait, Options } from "@biconomy/useAA";
import { polygonAmoy } from "viem/chains";
import { encodeFunctionData, parseAbi } from "wagmi";

export const UseSessionWithDistributedKey = ({ smartAccountAddress }) => {
  const { mutate, data: userOpResponse, error, isPending } = useSessionWithDistributedKey();

  const {
    isLoading: waitIsLoading,
    isSuccess: waitIsSuccess,
    error: waitError,
    data: waitData,
  } = useUserOpWait(userOpResponse);

  const mintTx = () =>
    mutate({
      transactions: {
        to: "0x1758f42Af7026fBbB559Dc60EcE0De3ef81f665e",
        data: encodeFunctionData({
          abi: parseAbi(["function safeMint(address _to)"]),
          functionName: "safeMint",
          args: [smartAccountAddress],
        }),
      },
      options: Options.Sponsored,
      smartAccountAddress
    });

  useEffect(() => {
    if (waitData?.success === "true") {
      console.log(waitData?.receipt?.transactionHash);
    }
  }, [waitData]);

  return (
    <ErrorGuard errors={[error, waitError]}>
      <Button
        title="Use Distributed Session to Mint"
        onClickFunc={mintTx}
        isLoading={isPending || waitIsLoading}
      />
    </ErrorGuard>
  );
};
```

## Parameters

```ts
type Transaction = {
  to: string;
  value: BigNumberish | string;
  data: string;
};
type UseSessionWithDistributedKeyProps = {
  /** The BuildUserOpOptions options. See https://bcnmy.github.io/biconomy-client-sdk/types/BuildUserOpOptions.html for further detail */
  options?: BuildUserOpOptions;
  /** The whitelisted transaction */
  transactions: Transaction | Transaction[];
  /** The index of the relevant session leaf. Defaults to zero */
  correspondingIndex?: number;
  /** The smart account address to be used for the session. Defaults to the connected smartAccount. */
  smartAccountAddress?: Hex
};
```

## Returns

```ts twoslash
import type { UserOpReceipt, UserOpStatus } from "@biconomy/account";
// ---cut---
type UserOpResponse = {
  userOpHash: string;
  wait(_confirmations?: number): Promise<UserOpReceipt>;
  waitForTxHash(): Promise<UserOpStatus>;
};
```


## Source

[hooks/useSessionWithDistributedKey.ts:87](https://github.com/bcnmy/useAA/blob/main/src/hooks/useSessionWithDistributedKey.ts#L87)
