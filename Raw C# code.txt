using System;
using System.Runtime.InteropServices;
using System.Threading;

namespace AutoMiner
{
    class Program
    {
        // Import user32.dll for hotkey registration and mouse input
        [DllImport("user32.dll", SetLastError = true)]
        private static extern bool RegisterHotKey(IntPtr hWnd, int id, uint fsModifiers, uint vk);

        [DllImport("user32.dll", SetLastError = true)]
        private static extern bool UnregisterHotKey(IntPtr hWnd, int id);

        [DllImport("user32.dll", CharSet = CharSet.Auto, CallingConvention = CallingConvention.StdCall)]
        public static extern void mouse_event(uint dwFlags, uint dx, uint dy, uint cButtons, uint dwExtraInfo);

        // Mouse event constants
        private const int MOUSEEVENTF_LEFTDOWN = 0x02;
        private const int MOUSEEVENTF_LEFTUP = 0x04;

        // Hotkey constants
        private const uint MOD_NONE = 0x0000; // No modifier
        private const uint VK_BACKSLASH = 0xDC; // Virtual key for '\'
        private const uint VK_RBRACKET = 0xDD; // Virtual key for ']'

        private const int WM_HOTKEY = 0x0312; // Windows message for hotkey

        static bool isMining = false;
        static int miningTime = 750; // Default mining time for Iron Pickaxe

        static void Main(string[] args)
        {
            // Set the console title
            Console.Title = "Auto Miner";

            Console.WriteLine("Auto Miner");
            Console.WriteLine("Press '\\' to start mining, ']' to stop mining.");
            Console.WriteLine("Enter the type of pickaxe (wooden, stone, iron, diamond):");

            string pickaxeType = Console.ReadLine().ToLower();
            SetMiningTime(pickaxeType);

            // Register hotkeys
            RegisterHotKey(IntPtr.Zero, 1, MOD_NONE, VK_BACKSLASH); // '\' key
            RegisterHotKey(IntPtr.Zero, 2, MOD_NONE, VK_RBRACKET); // ']' key

            Thread miningThread = new Thread(StartMining);
            miningThread.Start();

            // Message loop to capture hotkeys
            while (true)
            {
                NativeMessage msg;
                if (PeekMessage(out msg, IntPtr.Zero, 0, 0, 1))
                {
                    if (msg.message == WM_HOTKEY)
                    {
                        int id = msg.wParam.ToInt32();
                        if (id == 1) // '\' key
                        {
                            if (!isMining)
                            {
                                Console.Clear();
                                Console.WriteLine("Mining started...");
                                isMining = true;
                            }
                        }
                        else if (id == 2) // ']' key
                        {
                            if (isMining)
                            {
                                Console.Clear();
                                Console.WriteLine("Mining stopped.");
                                isMining = false;
                            }
                        }
                    }
                }

                Thread.Sleep(10); // Reduce CPU usage
            }
        }

        static void SetMiningTime(string pickaxeType)
        {
            switch (pickaxeType)
            {
                case "wooden":
                    miningTime = 1500;
                    break;
                case "stone":
                    miningTime = 1000;
                    break;
                case "iron":
                    miningTime = 750;
                    break;
                case "diamond":
                    miningTime = 500;
                    break;
                default:
                    Console.WriteLine("Invalid pickaxe type. Defaulting to iron.");
                    miningTime = 750;
                    break;
            }

            Console.WriteLine($"Mining time set to {miningTime}ms for {pickaxeType} pickaxe.");
        }

        static void StartMining()
        {
            while (true)
            {
                if (isMining)
                {
                    Console.WriteLine("Mining cobblestone...");
                    mouse_event(MOUSEEVENTF_LEFTDOWN, 0, 0, 0, 0);
                    Thread.Sleep(miningTime);
                    mouse_event(MOUSEEVENTF_LEFTUP, 0, 0, 0, 0);
                    Thread.Sleep(100);
                }
                else
                {
                    Thread.Sleep(100);
                }
            }
        }

        [StructLayout(LayoutKind.Sequential)]
        public struct NativeMessage
        {
            public IntPtr handle;
            public uint message;
            public IntPtr wParam;
            public IntPtr lParam;
            public uint time;
            public Point p;
        }

        [DllImport("user32.dll")]
        private static extern bool PeekMessage(out NativeMessage lpMsg, IntPtr hWnd, uint wMsgFilterMin, uint wMsgFilterMax, uint wRemoveMsg);

        public struct Point
        {
            public int X;
            public int Y;

            public Point(int x, int y)
            {
                X = x;
                Y = y;
            }
        }
    }
}
