import random
import time

class CODMobileSimulator:
    def __init__(self):
        self.player_hp = 100
        self.enemy_hp = 100
        self.scorestreak_points = 0
        self.ammo = 30
        self.max_ammo = 30
        
        # Fixed accuracy metrics and weapon stats
        self.loadouts = {
            "1": {"name": "M4 (Assault Rifle)", "damage": (15, 25), "accuracy": 0.8, "ammo": 30},
            "2": {"name": "DL Q33 (Sniper)", "damage": (50, 95), "accuracy": 0.5, "ammo": 5},
            "3": {"name": "Fennec (SMG)", "damage": (10, 18), "accuracy": 0.9, "ammo": 40}
        }
        self.selected_weapon = None

    def display_health_bar(self, label, hp):
        # Generates a visual progress bar [||||||....]
        bars = int(hp / 10)
        bar_str = "█" * bars + "░" * (10 - bars)
        return f"{label}: [{bar_str}] {hp}/100 HP"

    def choose_loadout(self):
        print("=" * 60)
        print("       💥 WELCOME TO CALL OF DUTY: MOBILE (IDLE EDITION) 💥")
        print("=" * 60)
        print("\nSelect your Loadout Weapon:")
        
        for key, weapon in self.loadouts.items():
            low_dmg, high_dmg = weapon['damage']
            acc = int(weapon['accuracy'] * 100)
            print(f" [{key}] {weapon['name']:<22} | Damage: {low_dmg}-{high_dmg:<2} | Accuracy: {acc}% | Ammo: {weapon['ammo']}")
        print("-" * 60)
        
        choice = input("Enter weapon number (1-3): ").strip()
        if choice in self.loadouts:
            self.selected_weapon = self.loadouts[choice]
        else:
            print("\n⚠️ Invalid selection! Defaulting to M4.")
            self.selected_weapon = self.loadouts["1"]
            
        self.ammo = self.selected_weapon["ammo"]
        self.max_ammo = self.selected_weapon["ammo"]
        
        print(f"\n🔒 Locked in: {self.selected_weapon['name']}!")
        print("🚀 Deploying to Standoff map...")
        time.sleep(1.5)

    def start_match(self):
        self.choose_loadout()
        print("\n🎵 *Guitar Riff* MATCH START: TEAM DEATHMATCH 🎵")
        print("=" * 60)
        time.sleep(1)

        while self.player_hp > 0 and self.enemy_hp > 0:
            # HUD Status Display
            print("\n" + "-" * 40)
            print(self.display_health_bar("PLAYER", self.player_hp) + f"  |  🎒 Ammo: {self.ammo}/{self.max_ammo}")
            print(self.display_health_bar("ENEMY ", self.enemy_hp))
            print("-" * 40)
            
            action = input("Actions: [F] Fire | [R] Reload | [S] Tactical Slide -> ").lower().strip()
            print() # Visual spacing

            # --- PLAYER ACTION PHASE ---
            if action == 'f':
                if self.ammo <= 0:
                    print("🚫 *CLICK CLICK* OUT OF AMMO! You need to Reload [R]!")
                    player_fired = False
                else:
                    self.ammo -= 1
                    player_fired = True
                    # Check accuracy roll
                    if random.random() < self.selected_weapon["accuracy"]:
                        damage = random.randint(*self.selected_weapon["damage"])
                        
                        # Randomly trigger a critical headshot
                        if random.random() < 0.20: 
                            damage = int(damage * 1.5)
                            print(f"🎯 HEADSHOT!! You absolutely beamed them for {damage} damage!")
                        else:
                            print(f"💥 HIT! You dealt {damage} damage to the enemy.")
                            
                        self.enemy_hp -= damage
                        self.scorestreak_points += 25
                    else:
                        print("❌ MISS! Your shots went wide into the wall.")
                        
            elif action == 'r':
                print("🔄 Reloading... Cover me!")
                time.sleep(1)
                self.ammo = self.max_ammo
                print("👍 Ammo Maxed Out!")
                player_fired = False
                
            elif action == 's':
                print("🏃💨 Tactical Slide! You dodged low and made yourself a harder target.")
                player_fired = False
            else:
                print("⚠️ Panicked input! You tripped over your own tactical gear!")
                player_fired = False

            # Check if enemy died before they can shoot back
            if self.enemy_hp <= 0:
                break

            # --- ENEMY COUNTER-ATTACK PHASE ---
            time.sleep(1)
            # Enemy accuracy drops significantly if player slides or if player didn't shoot back
            if action == 's':
                enemy_accuracy = 0.25
            elif not player_fired:
                enemy_accuracy = 0.40 # Harder to track someone who isn't standing and shooting
            else:
                enemy_accuracy = 0.60

            if random.random() < enemy_accuracy:
                enemy_damage = random.randint(12, 25)
                self.player_hp -= enemy_damage
                print(f"🚨 OUCH! You got hit! Enemy dealt {enemy_damage} damage.")
            else:
                print("🛡️ Enemy missed! You saw the tracers pass right by your head.")

            # Scorestreak Check
            if self.scorestreak_points >= 75:
                print("\n🛸 🟥 SCORESTREAK READY: UAV INBOUND! Enemy tracking enhanced! 🟥")
                # Temporarily buff player accuracy for the next turn
                self.selected_weapon["accuracy"] = min(1.0, self.selected_weapon["accuracy"] + 0.1)
                self.scorestreak_points = 0 

        # --- MATCH OUTCOME ---
        print("\n" + "=" * 60)
        if self.player_hp > 0:
            print("🏆 VICTORY! Enemy eliminated. +100 XP. Clear MVP! 🏆")
            print("       Final Stats: Remaining HP: " + str(self.player_hp))
        else:
            print("💀 DEFEATED! We'll get 'em next time. 💀")
        print("=" * 60)

if __name__ == "__main__":
    game = CODMobileSimulator()
    game.start_match()
