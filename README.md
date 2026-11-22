# task14using System;
using System.Drawing;
using System.Windows.Forms;

namespace SnakeAndApple
{
    public partial class Form1 : Form
    {
        Random rnd = new Random();

        int xal = 0;              // toplanan xal
        int suret = 5;            // ilan hər addımda neçə piksel gedir
        int saniye = 0;           // alma/bomba ekranda neçə saniyədir durur
        string istiqamet = "sag"; // ilanın getdiyi tərəf
        bool oyunBitdi = false;

        public Form1()
        {
            InitializeComponent();

            // Şəkillər proqramın yanındakı fayllardan oxunur
            pictureBox1.Image = Image.FromFile("ilan.png");
            pictureBox2.Image = Image.FromFile("alma.png");
            pictureBox3.Image = Image.FromFile("bomba.png");

            this.KeyPreview = true;
            this.KeyDown += Form1_KeyDown;
            timer1.Tick += timer1_Tick;
            timer2.Tick += timer2_Tick;

            timer1.Interval = 50;    // ilanın hərəkəti
            timer2.Interval = 1000;  // 1 saniyə

            YeniOyun();
        }

        // Yeni oyun: xal və sürət sıfırlanır
        void YeniOyun()
        {
            xal = 0;
            suret = 5;
            istiqamet = "sag";
            oyunBitdi = false;

            pictureBox1.Left = 100;
            pictureBox1.Top = 100;

            label1.Text = "Xal: 0";

            YeniYem();

            timer1.Start();
            timer2.Start();
        }

        // Alma və ya bombanı random yerə atır
        void YeniYem()
        {
            saniye = 0;

            int x = rnd.Next(0, this.ClientSize.Width - pictureBox2.Width);
            int y = rnd.Next(30, this.ClientSize.Height - pictureBox2.Height);

            // 4-dən 1 ehtimalla bomba çıxır
            if (rnd.Next(0, 4) == 0)
            {
                pictureBox3.Left = x;
                pictureBox3.Top = y;
                pictureBox3.Visible = true;   // bomba görünür
                pictureBox2.Visible = false;  // alma gizlənir
            }
            else
            {
                pictureBox2.Left = x;
                pictureBox2.Top = y;
                pictureBox2.Visible = true;   // alma görünür
                pictureBox3.Visible = false;  // bomba gizlənir
            }
        }

        void OyunuBitir()
        {
            timer1.Stop();
            timer2.Stop();
            oyunBitdi = true;

            MessageBox.Show("Oyun bitdi! Bombanı yedin.\nSənin xalın: " + xal +
                            "\n\nYeni oyun üçün Enter bas.");
        }

        // İlanın hərəkəti
        private void timer1_Tick(object sender, EventArgs e)
        {
            if (istiqamet == "sag") pictureBox1.Left += suret;
            if (istiqamet == "sol") pictureBox1.Left -= suret;
            if (istiqamet == "yuxari") pictureBox1.Top -= suret;
            if (istiqamet == "asagi") pictureBox1.Top += suret;

            // Ekrandan çıxanda o biri tərəfdən girir
            if (pictureBox1.Left > this.ClientSize.Width) pictureBox1.Left = 0;
            if (pictureBox1.Left < 0) pictureBox1.Left = this.ClientSize.Width - pictureBox1.Width;
            if (pictureBox1.Top > this.ClientSize.Height) pictureBox1.Top = 0;
            if (pictureBox1.Top < 0) pictureBox1.Top = this.ClientSize.Height - pictureBox1.Height;

            // Almanı yedi
            if (pictureBox2.Visible == true && pictureBox1.Bounds.IntersectsWith(pictureBox2.Bounds))
            {
                xal = xal + 1;
                suret = suret + 1;   // sürət artır
                label1.Text = "Xal: " + xal;
                YeniYem();
            }

            // Bombanı yedi
            if (pictureBox3.Visible == true && pictureBox1.Bounds.IntersectsWith(pictureBox3.Bounds))
            {
                OyunuBitir();
            }
        }

        // Hər saniyə işləyir: 5 saniyə keçəndə alma/bomba yerini dəyişir
        private void timer2_Tick(object sender, EventArgs e)
        {
            saniye = saniye + 1;
            label1.Text = "Xal: " + xal + "    Vaxt: " + (5 - saniye);

            if (saniye >= 5)
            {
                YeniYem();
            }
        }

        // Klaviatura
        private void Form1_KeyDown(object sender, KeyEventArgs e)
        {
            if (e.KeyCode == Keys.Enter)
            {
                YeniOyun();
            }

            if (oyunBitdi == true) return;

            if (e.KeyCode == Keys.Right) istiqamet = "sag";
            if (e.KeyCode == Keys.Left) istiqamet = "sol";
            if (e.KeyCode == Keys.Up) istiqamet = "yuxari";
            if (e.KeyCode == Keys.Down) istiqamet = "asagi";
        }
    }
}