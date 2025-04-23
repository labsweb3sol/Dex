# Dex
class LabsWeb3:
    def __init__(self, initial_liquidity=None):
        self.liquidity_pools = initial_liquidity if initial_liquidity else {}
        print("Welcome to LabsWeb3 - Your Decentralized Swap Platform (Simulation)")

    def add_liquidity(self, token_a, token_b, amount_a, amount_b):
        pair = tuple(sorted((token_a, token_b)))
        if pair not in self.liquidity_pools:
            self.liquidity_pools[pair] = {'token_a': 0, 'token_b': 0}
        self.liquidity_pools[pair]['token_a'] += amount_a
        self.liquidity_pools[pair]['token_b'] += amount_b
        print(f"Added {amount_a} {token_a} and {amount_b} {token_b} to the {token_a}-{token_b} pool.")

    def get_swap_rate(self, token_in, token_out, amount_in):
        pair = tuple(sorted((token_in, token_out)))
        if pair not in self.liquidity_pools:
            print(f"Liquidity pool for {token_in}-{token_out} not found.")
            return None

        pool = self.liquidity_pools[pair]
        reserve_in = pool['token_a'] if pair[0] == token_in else pool['token_b']
        reserve_out = pool['token_b'] if pair[1] == token_out else pool['token_a']

        if reserve_in == 0 or reserve_out == 0:
            print("Insufficient liquidity.")
            return None

        # Simple constant product formula (x * y = k)
        constant = reserve_in * reserve_out
        new_reserve_in = reserve_in + amount_in
        new_reserve_out = constant / new_reserve_in
        amount_out = reserve_out - new_reserve_out
        return amount_out

    def swap(self, token_in, token_out, amount_in):
        amount_out = self.get_swap_rate(token_in, token_out, amount_in)
        if amount_out is not None:
            pair = tuple(sorted((token_in, token_out)))
            pool = self.liquidity_pools[pair]
            if pair[0] == token_in:
                pool['token_a'] += amount_in
                pool['token_b'] -= amount_out
            else:
                pool['token_b'] += amount_in
                pool['token_a'] -= amount_out
            print(f"Swapped {amount_in} {token_in} for {amount_out} {token_out}.")
            return amount_out
        else:
            print("Swap failed.")
            return None

    def view_liquidity_pools(self):
        print("\n--- Current Liquidity Pools ---")
        for pair, liquidity in self.liquidity_pools.items():
            token_a, token_b = pair
            print(f"{token_a}-{token_b}: {liquidity['token_a']} {token_a}, {liquidity['token_b']} {token_b}")
        print("-----------------------------\n")

# Example Usage (Simulation)
if __name__ == "__main__":
    labs_web3 = LabsWeb3(initial_liquidity={
        ('SOL', 'USDC'): {'token_a': 100, 'token_b': 1000},
        ('ETH', 'USDC'): {'token_a': 50, 'token_b': 10000}
    })

    labs_web3.view_liquidity_pools()
