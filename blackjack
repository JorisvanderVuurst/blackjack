import tkinter as tk
from tkinter import messagebox, simpledialog
import random
import os
from PIL import Image, ImageTk, ImageDraw, ImageFont
import time

def generate_cards():
    if not os.path.exists("cards"): os.makedirs("cards")
    card_back = Image.new('RGB', (80, 120), (0, 0, 128))
    draw = ImageDraw.Draw(card_back)
    draw.rectangle([(5, 5), (75, 115)], outline=(255, 215, 0), width=2)
    for x in range(10, 70, 10):
        for y in range(10, 110, 10):
            draw.rectangle([(x, y), (x+5, y+5)], fill=(255, 215, 0))
    card_back.save("cards/card_back.png")
    
    suits = {"hearts": "♥", "diamonds": "♦", "clubs": "♣", "spades": "♠"}
    values = ["2", "3", "4", "5", "6", "7", "8", "9", "10", "jack", "queen", "king", "ace"]
    try: font, small_font = ImageFont.truetype("arial.ttf", 20), ImageFont.truetype("arial.ttf", 14)
    except: font, small_font = ImageFont.load_default(), ImageFont.load_default()
    
    for suit, symbol in suits.items():
        color = (255, 0, 0) if suit in ["hearts", "diamonds"] else (0, 0, 0)
        for value in values:
            card = Image.new('RGB', (80, 120), (255, 255, 255))
            draw = ImageDraw.Draw(card)
            draw.rectangle([(0, 0), (79, 119)], outline=(0, 0, 0), width=1)
            display = value[0].upper() if value != "10" else "10"
            if value in ["jack", "queen", "king"]: display = value[0].upper()
            elif value == "ace": display = "A"
            draw.text((5, 5), display, fill=color, font=small_font)
            draw.text((5, 20), symbol, fill=color, font=small_font)
            draw.text((65, 95), display, fill=color, font=small_font)
            draw.text((65, 80), symbol, fill=color, font=small_font)
            
            if value in ["jack", "queen", "king"]:
                draw.text((30, 50), value.upper(), fill=color, font=font)
                draw.text((35, 70), symbol, fill=color, font=font)
            elif value == "ace":
                draw.text((30, 50), symbol, fill=color, font=ImageFont.truetype("arial.ttf", 40) if os.path.exists("arial.ttf") else font)
            else:
                draw.text((30, 50), value, fill=color, font=font)
                draw.text((35, 70), symbol, fill=color, font=font)
            card.save(f"cards/{value}_of_{suit}.png")

class Card:
    def __init__(self, suit, value):
        self.suit, self.value = suit, value
    def get_value(self):
        return 10 if self.value in ["Jack", "Queen", "King"] else 11 if self.value == "Ace" else int(self.value)
    def get_image_name(self):
        suit_map = {"Hearts": "hearts", "Diamonds": "diamonds", "Clubs": "clubs", "Spades": "spades"}
        value_map = {"Jack": "jack", "Queen": "queen", "King": "king", "Ace": "ace"}
        return f"{value_map.get(self.value, self.value)}_of_{suit_map[self.suit]}.png"

class Hand:
    def __init__(self):
        self.cards = []
    def add_card(self, card):
        self.cards.append(card)
    def calculate_value(self):
        value = sum(card.get_value() for card in self.cards)
        aces = sum(1 for card in self.cards if card.value == "Ace")
        while value > 21 and aces: value, aces = value - 10, aces - 1
        return value
    def clear(self):
        self.cards = []

class BlackjackGUI:
    def __init__(self, root):
        self.root = root
        self.root.title("Blackjack")
        self.root.geometry("800x600")
        self.root.configure(bg="#2c8c2c")
        
        self.deck = []
        self.build_deck()
        random.shuffle(self.deck)
        self.player_hand, self.dealer_hand = Hand(), Hand()
        self.player_chips, self.current_bet, self.game_over = 100, 0, False
        
        if not os.path.exists("cards") or len(os.listdir("cards")) < 52:
            generate_cards()
        
        try: self.card_back = ImageTk.PhotoImage(Image.open("cards/card_back.png").resize((80, 120)))
        except: self.card_back = ImageTk.PhotoImage(Image.new('RGB', (80, 120), color=(200, 200, 200)))
        
        self.card_images = {}
        
        self.title_label = tk.Label(root, text="BLACKJACK", font=("Arial", 24, "bold"), bg="#2c8c2c", fg="white")
        self.title_label.pack(pady=10)
        
        self.dealer_frame = tk.Frame(root, bg="#2c8c2c")
        self.dealer_frame.pack(pady=10)
        self.dealer_label = tk.Label(self.dealer_frame, text="Dealer: ", font=("Arial", 14), bg="#2c8c2c", fg="white")
        self.dealer_label.pack(side=tk.LEFT, padx=5)
        self.dealer_score = tk.Label(self.dealer_frame, text="", font=("Arial", 14), bg="#2c8c2c", fg="white")
        self.dealer_score.pack(side=tk.LEFT)
        
        self.dealer_cards_frame = tk.Frame(root, bg="#2c8c2c")
        self.dealer_cards_frame.pack(pady=5)
        
        self.player_cards_frame = tk.Frame(root, bg="#2c8c2c")
        self.player_cards_frame.pack(pady=5)
        
        self.player_frame = tk.Frame(root, bg="#2c8c2c")
        self.player_frame.pack(pady=10)
        self.player_label = tk.Label(self.player_frame, text="Player: ", font=("Arial", 14), bg="#2c8c2c", fg="white")
        self.player_label.pack(side=tk.LEFT, padx=5)
        self.player_score = tk.Label(self.player_frame, text="", font=("Arial", 14), bg="#2c8c2c", fg="white")
        self.player_score.pack(side=tk.LEFT)
        
        self.chips_frame = tk.Frame(root, bg="#2c8c2c")
        self.chips_frame.pack(pady=10)
        self.chips_label = tk.Label(self.chips_frame, text=f"Chips: {self.player_chips}", font=("Arial", 12), bg="#2c8c2c", fg="white")
        self.chips_label.pack(side=tk.LEFT, padx=20)
        self.bet_label = tk.Label(self.chips_frame, text=f"Bet: {self.current_bet}", font=("Arial", 12), bg="#2c8c2c", fg="white")
        self.bet_label.pack(side=tk.LEFT, padx=20)
        
        self.message = tk.Label(root, text="", font=("Arial", 14), bg="#2c8c2c", fg="yellow")
        self.message.pack(pady=10)
        
        self.buttons_frame = tk.Frame(root, bg="#2c8c2c")
        self.buttons_frame.pack(pady=10)
        self.hit_button = tk.Button(self.buttons_frame, text="Hit", font=("Arial", 12), command=self.hit)
        self.hit_button.pack(side=tk.LEFT, padx=10)
        self.stand_button = tk.Button(self.buttons_frame, text="Stand", font=("Arial", 12), command=self.stand)
        self.stand_button.pack(side=tk.LEFT, padx=10)
        self.new_game_button = tk.Button(self.buttons_frame, text="New Game", font=("Arial", 12), command=self.new_game)
        self.new_game_button.pack(side=tk.LEFT, padx=10)
        
        self.new_game()
    
    def build_deck(self):
        suits = ["Hearts", "Diamonds", "Clubs", "Spades"]
        values = ["2", "3", "4", "5", "6", "7", "8", "9", "10", "Jack", "Queen", "King", "Ace"]
        self.deck = [Card(suit, value) for suit in suits for value in values]
    
    def deal(self):
        if not self.deck:
            self.build_deck()
            random.shuffle(self.deck)
        return self.deck.pop()
    
    def get_card_image(self, card):
        if card is None: return self.card_back
        card_name = card.get_image_name()
        if card_name not in self.card_images:
            try: self.card_images[card_name] = ImageTk.PhotoImage(Image.open(f"cards/{card_name}").resize((80, 120)))
            except: self.card_images[card_name] = ImageTk.PhotoImage(Image.new('RGB', (80, 120), color=(200, 200, 200)))
        return self.card_images[card_name]
    
    def update_display(self):
        for widget in self.dealer_cards_frame.winfo_children(): widget.destroy()
        for widget in self.player_cards_frame.winfo_children(): widget.destroy()
        
        if self.game_over:
            for card in self.dealer_hand.cards:
                img = self.get_card_image(card)
                lbl = tk.Label(self.dealer_cards_frame, image=img, bg="#2c8c2c")
                lbl.image = img
                lbl.pack(side=tk.LEFT, padx=5)
        else:
            if self.dealer_hand.cards:
                img = self.card_back
                lbl = tk.Label(self.dealer_cards_frame, image=img, bg="#2c8c2c")
                lbl.image = img
                lbl.pack(side=tk.LEFT, padx=5)
                
                for card in self.dealer_hand.cards[1:]:
                    img = self.get_card_image(card)
                    lbl = tk.Label(self.dealer_cards_frame, image=img, bg="#2c8c2c")
                    lbl.image = img
                    lbl.pack(side=tk.LEFT, padx=5)
        
        for card in self.player_hand.cards:
            img = self.get_card_image(card)
            lbl = tk.Label(self.player_cards_frame, image=img, bg="#2c8c2c")
            lbl.image = img
            lbl.pack(side=tk.LEFT, padx=5)
        
        if self.game_over:
            self.dealer_score.config(text=str(self.dealer_hand.calculate_value()))
        else:
            if len(self.dealer_hand.cards) > 1:
                visible_value = sum(card.get_value() for card in self.dealer_hand.cards[1:])
                self.dealer_score.config(text=f"{visible_value}+")
            else:
                self.dealer_score.config(text="")
        
        self.player_score.config(text=str(self.player_hand.calculate_value()))
    
    def new_game(self):
        self.player_hand.clear()
        self.dealer_hand.clear()
        self.game_over = False
        
        bet = simpledialog.askinteger("Place Your Bet", f"You have {self.player_chips} chips.\nHow many chips would you like to bet?",
                                      minvalue=1, maxvalue=self.player_chips)
        self.current_bet = 1 if bet is None else bet
        self.bet_label.config(text=f"Bet: {self.current_bet}")
        
        self.player_hand.add_card(self.deal())
        self.dealer_hand.add_card(self.deal())
        self.player_hand.add_card(self.deal())
        self.dealer_hand.add_card(self.deal())
        
        self.update_display()
        self.message.config(text="Your turn! Hit or Stand?")
        
        self.hit_button.config(state=tk.NORMAL)
        self.stand_button.config(state=tk.NORMAL)
        self.new_game_button.config(state=tk.DISABLED)
        
        if self.player_hand.calculate_value() == 21:
            self.stand()
    
    def hit(self):
        self.player_hand.add_card(self.deal())
        self.update_display()
        
        if self.player_hand.calculate_value() > 21:
            self.game_over = True
            self.message.config(text="Bust! You lose.")
            self.end_game("dealer")
    
    def stand(self):
        self.game_over = True
        self.update_display()
        
        while self.dealer_hand.calculate_value() < 17:
            self.dealer_hand.add_card(self.deal())
            self.update_display()
            self.root.update()
            time.sleep(0.5)
        
        player_value = self.player_hand.calculate_value()
        dealer_value = self.dealer_hand.calculate_value()
        
        if dealer_value > 21:
            self.message.config(text="Dealer busts! You win!")
            self.end_game("player")
        elif dealer_value > player_value:
            self.message.config(text="Dealer wins!")
            self.end_game("dealer")
        elif dealer_value < player_value:
            self.message.config(text="You win!")
            self.end_game("player")
        else:
            self.message.config(text="Push! It's a tie.")
            self.end_game("push")
    
    def end_game(self, winner):
        if winner == "player": self.player_chips += self.current_bet
        elif winner == "dealer": self.player_chips -= self.current_bet
        
        self.chips_label.config(text=f"Chips: {self.player_chips}")
        
        self.hit_button.config(state=tk.DISABLED)
        self.stand_button.config(state=tk.DISABLED)
        self.new_game_button.config(state=tk.NORMAL)
        
        if self.player_chips <= 0:
            messagebox.showinfo("Game Over", "You're out of chips! Game over.")
            if messagebox.askyesno("Restart", "Would you like to start over with 100 chips?"):
                self.player_chips = 100
                self.chips_label.config(text=f"Chips: {self.player_chips}")
            else:
                self.root.quit()

if __name__ == "__main__":
    root = tk.Tk()
    game = BlackjackGUI(root)
    root.mainloop()
