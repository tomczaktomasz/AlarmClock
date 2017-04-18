# AlarmClock
//AlarmClock used to remember do something for exampole check Toilet pare hour


using System;

using System.Collections.Generic;

using System.ComponentModel;

using System.Data;

using System.Drawing;

using System.Linq;

using System.Text;

using System.Windows.Forms;

using System.Threading;








namespace WCTimer




{

    public partial class Form1 : Form
    
    {
        public delegate void ThreadingWork(string value, TextBox c);        
        
        bool _canClose;
        Thread TH1;
        DateTime Time_Clock = new DateTime();
        bool s = true;
        
        private void AppendTextBox(string value, TextBox c)
        {
            if (InvokeRequired) // 
            {
                ThreadingWork Metod = new ThreadingWork(AppendTextBox);
                this.BeginInvoke(Metod, new object[] { value, c });     // Invoke dla funkcji z wieloma argumentami ^ porzebny jest zdeklaorwanie delegata 
                return;

                //this.Invoke(new Action<string>(AppendTextBox), new object[] { value }); // Invoke z jednym argumentem
                //return;
            }

            c.Text += value; // obliczenia funkcji void AppendTextBox

        }
        private void TimerCheck()
        {
            DateTime Before_Time_TH = new DateTime();
            Before_Time_TH = DateTime.Now;
            AppendTextBox("****START Alarm WC - " + Convert.ToString(Before_Time_TH) + " *****" + System.Environment.NewLine, Log_textBox);

            if (Before_Time_TH.Minute <= 28)
            {
                AppendTextBox("****Alarm wstrzymany (" + Convert.ToString(28 - Before_Time_TH.Minute) + " min) - " + Convert.ToString(Before_Time_TH) +" *****" + Environment.NewLine, Log_textBox);
                Thread.Sleep((1000 * 60) * (28 - Before_Time_TH.Minute));
                Time_Clock = DateTime.Now;
                AppendTextBox("****Alarm wznowiony - " + Convert.ToString(Time_Clock) + " *****" + Environment.NewLine, Log_textBox);

            }
            else 
            {

                AppendTextBox("****Alarm wstrzymany (" + Convert.ToString(88 - Before_Time_TH.Minute) + " min) - " + Convert.ToString(Before_Time_TH) + " *****" + Environment.NewLine, Log_textBox);
                Thread.Sleep((1000 * 60) * (88 - Before_Time_TH.Minute));
                Time_Clock = DateTime.Now;
                AppendTextBox("****Alarm wznowiony - " + Convert.ToString(Time_Clock) + " *****" + Environment.NewLine, Log_textBox);
            }
            
            while (s)
            {
                Time_Clock = DateTime.Now;
                if (Time_Clock.Minute == 30 && Time_Clock.Second == 0)
                {
                    AppendTextBox("Wykonano zadanie: " + Time_Clock.ToString() + System.Environment.NewLine, Log_textBox);
                    
                    MessageBox.Show("Sprawdź czystość Toalet i podpisz listę kontrolną na godz. "+ Convert.ToString(Time_Clock.Hour)+":30!");
                    //s = false;
                    AppendTextBox("****Alarm wstrzymany (58 min)***** " + System.Environment.NewLine, Log_textBox);
                    Thread.Sleep( ((1000*60)*58) + (1000*53));
                    AppendTextBox("****Alarm wznowiony - "+ Convert.ToString(Time_Clock) +" ***** " + System.Environment.NewLine, Log_textBox);
                    
                }

            }
 
        }
        public Form1()
        {
            MaximizeBox = false;
            MinimizeBox = false;
            InitializeComponent();
        }

        private void WCTimer_notifyIcon_DoubleClick(object sender, EventArgs e)
        {
            Show();
        }

        private void Form1_FormClosing(object sender, FormClosingEventArgs e)
        {
            if (_canClose)
                return;
            e.Cancel = true;
            Hide();
        }

        private void CloseAplication_Button_Click(object sender, EventArgs e)
        {
            _canClose = true;

            TH1.Abort();
            Close();
        }

        private void Start_alarm_Button_Click(object sender, EventArgs e)
        {
            Start_alarm_Button.Enabled = false;
            Stop_alarm_button.Enabled = true;
            TH1 = new Thread(TimerCheck);
            TH1.Start();
            
        }

        private void Stop_alarm_button_Click(object sender, EventArgs e)
        {
        
            Log_textBox.Text += "****STOP Alarm WC*****" + System.Environment.NewLine;
          
            TH1.Abort();
            Stop_alarm_button.Enabled = false;
            Start_alarm_Button.Enabled = true;

        }
    }
}
