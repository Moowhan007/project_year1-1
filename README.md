# project_year1-1
งานโปรเจคแนวคิดคอม ปี1เทอม1
import random
import tkinter as tk
from tkinter import messagebox
import random

class RandomGameGUI:
    def __init__(self, root):
        self.root = root
        self.root.title("Random Number Game")
        self.root.geometry("400x500")
        self.root.config(padx=20, pady=20)
        

        self.player_name = ""
        self.player_score = 0
        self.bot_score = 0
        

        self.allowed_chars = set(
            "abcdefghijklmnopqrstuvwxyzABCDEFGHIJKLMNOPQRSTUVWXYZ"
            "กขฃคฅฆงจฉชซฌญฎฏฐฑฒณดตถทธนบปผฝพฟภมยรฤลฦวศษสหฬอฮ"
            "ะัาำิีึืุูเแโใไๅ็่้๊๋์ "
        )


        self.name_frame = tk.Frame(self.root)
        self.game_frame = tk.Frame(self.root)
        self.summary_frame = tk.Frame(self.root)

        self.setup_name_frame()
        self.setup_game_frame()
        self.setup_summary_frame()


        self.name_frame.pack(fill="both", expand=True)

    def setup_name_frame(self):
        tk.Label(self.name_frame, text="ยินดีต้อนรับสู่เกมสุ่มตัวเลข", font=("Helvetica", 16, "bold")).pack(pady=20)
        tk.Label(self.name_frame, text="กรุณากรอกชื่อของท่าน\n(a-z, A-Z, ก-ฮ ห้ามใส่ตัวเลข)", font=("Helvetica", 10)).pack(pady=5)
        
        self.name_entry = tk.Entry(self.name_frame, font=("Helvetica", 14), justify="center")
        self.name_entry.pack(pady=10)
        

        self.name_entry.bind('<Return>', lambda event: self.start_game())
        
        tk.Button(self.name_frame, text="เริ่มเกม", font=("Helvetica", 12, "bold"), bg="#4CAF50", fg="white", 
                  command=self.start_game).pack(pady=20)

    def setup_game_frame(self):

        self.score_label = tk.Label(self.game_frame, text="", font=("Helvetica", 14, "bold"))
        self.score_label.pack(pady=10)

        tk.Label(self.game_frame, text="กรอกตัวเลข 0-9", font=("Helvetica", 14)).pack(pady=5)


        self.number_entry = tk.Entry(self.game_frame, font=("Helvetica", 14), justify="center", width=10)
        self.number_entry.pack(pady=10)
        

        self.number_entry.bind('<Return>', lambda event: self.submit_number())

        tk.Button(self.game_frame, text="ยืนยันตัวเลข", font=("Helvetica", 12, "bold"), bg="#4CAF50", fg="white",
                  command=self.submit_number).pack(pady=10)

        self.result_label = tk.Label(self.game_frame, text="รอการสุ่ม...", font=("Helvetica", 12), fg="blue")
        self.result_label.pack(pady=15)

        tk.Button(self.game_frame, text="จบเกมและดูสรุปผล", font=("Helvetica", 10), bg="#f44336", fg="white",
                  command=self.show_summary).pack(side="bottom", pady=20)

    def setup_summary_frame(self):
        self.summary_title = tk.Label(self.summary_frame, text="สรุปผลแพ้/ชนะ", font=("Helvetica", 18, "bold"))
        self.summary_title.pack(pady=20)
        
        self.summary_detail = tk.Label(self.summary_frame, text="", font=("Helvetica", 14))
        self.summary_detail.pack(pady=20)
        
        self.summary_result = tk.Label(self.summary_frame, text="", font=("Helvetica", 16, "bold"))
        self.summary_result.pack(pady=20)

        tk.Button(self.summary_frame, text="เล่นใหม่อีกครั้ง", font=("Helvetica", 12, "bold"), bg="#2196F3", fg="white",
                  command=self.reset_game).pack(pady=10)

    def start_game(self):
        name = self.name_entry.get().strip()
        if not name:
            messagebox.showerror("ข้อมูลไม่ถูกต้อง", "ชื่อต้องไม่เป็นค่าว่าง")
            return
        if not set(name) <= self.allowed_chars:
            messagebox.showerror("ข้อมูลไม่ถูกต้อง", "ชื่อต้องประกอบด้วย a-z, A-Z, ก-ฮ และการเว้นวรรคเท่านั้น\n(ห้ามพิมพ์ตัวเลข หรืออักขระพิเศษ)")
            return
        
        self.player_name = name
        self.update_score_display()
        
        self.name_frame.pack_forget()
        self.game_frame.pack(fill="both", expand=True)

        self.number_entry.focus()

    def submit_number(self):
        raw_input = self.number_entry.get().strip()
        
        try:
            player_num = int(raw_input)
            
            if not (0 <= player_num <= 9):
                raise ValueError("กรุณาระบุตัวเลขให้อยู่ในช่วง 0 ถึง 9 เท่านั้น")
                
            self.number_entry.delete(0, tk.END)
            self.play_round(player_num)
            
        except ValueError as e:
            if "ให้อยู่ในช่วง 0 ถึง 9" in str(e):
                error_msg = str(e)
            else:
                error_msg = "กรุณากรอกเป็นเลขจำนวนเต็ม 0-9 เท่านั้น!\n(ห้ามใส่ทศนิยม ข้อความ หรือเว้นว่าง)"
                
            messagebox.showerror("ข้อมูลไม่ถูกต้อง", error_msg)
            self.number_entry.delete(0, tk.END)

    def play_round(self, player_num):
        bot_num = random.randint(0, 9)
        
        if player_num > bot_num:
            self.player_score += 1
            result_text = "คุณได้ 1 คะแนน!"
            color = "green"
        elif player_num < bot_num:
            self.bot_score += 1
            result_text = "บอทได้ 1 คะแนน!"
            color = "red"
        else:
            result_text = "เสมอ!"
            color = "orange"

        self.result_label.config(text=f"คุณเลือก: {player_num} | บอทสุ่มได้: {bot_num}\n\n{result_text}", fg=color)
        self.update_score_display()

    def update_score_display(self):

        self.score_label.config(text="ยินดีต้อนรับเข้าสู่เกม!")

    def show_summary(self):
        self.game_frame.pack_forget()
        self.summary_frame.pack(fill="both", expand=True)
        
        self.summary_detail.config(text=f"ผู้เล่น {self.player_name} ได้คะแนน {self.player_score}\nบอทได้คะแนน {self.bot_score}")
        
        if self.player_score > self.bot_score:
            self.summary_result.config(text="ผู้เล่นชนะ!", fg="green")
        elif self.player_score < self.bot_score:
            self.summary_result.config(text="บอทชนะ!", fg="red")
        else:
            self.summary_result.config(text="เสมอ!", fg="orange")

    def reset_game(self):
        self.player_score = 0
        self.bot_score = 0
        self.result_label.config(text="รอการสุ่ม...", fg="blue")
        self.update_score_display()
        
        self.summary_frame.pack_forget()
        self.game_frame.pack(fill="both", expand=True)
        self.number_entry.focus()

if __name__ == "__main__":
    root = tk.Tk()
    app = RandomGameGUI(root)
    root.mainloop()
