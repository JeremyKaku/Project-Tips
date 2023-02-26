
 ```C#

 private static char[] HANKAKU_KATAKANA = { '｡', '｢', '｣', '､', '･',
      'ｦ', 'ｧ', 'ｨ', 'ｩ', 'ｪ', 'ｫ', 'ｬ', 'ｭ', 'ｮ', 'ｯ', 'ｰ', 'ｱ', 'ｲ',
      'ｳ', 'ｴ', 'ｵ', 'ｶ', 'ｷ', 'ｸ', 'ｹ', 'ｺ', 'ｻ', 'ｼ', 'ｽ', 'ｾ', 'ｿ',
      'ﾀ', 'ﾁ', 'ﾂ', 'ﾃ', 'ﾄ', 'ﾅ', 'ﾆ', 'ﾇ', 'ﾈ', 'ﾉ', 'ﾊ', 'ﾋ', 'ﾌ',
      'ﾍ', 'ﾎ', 'ﾏ', 'ﾐ', 'ﾑ', 'ﾒ', 'ﾓ', 'ﾔ', 'ﾕ', 'ﾖ', 'ﾗ', 'ﾘ', 'ﾙ',
      'ﾚ', 'ﾛ', 'ﾜ', 'ﾝ', 'ﾞ', 'ﾟ' };

    private static char[] ZENKAKU_KATAKANA = { '。', '「', '」', '、', '・',
      'ヲ', 'ァ', 'ィ', 'ゥ', 'ェ', 'ォ', 'ャ', 'ュ', 'ョ', 'ッ', 'ー', 'ア', 'イ',
      'ウ', 'エ', 'オ', 'カ', 'キ', 'ク', 'ケ', 'コ', 'サ', 'シ', 'ス', 'セ', 'ソ',
      'タ', 'チ', 'ツ', 'テ', 'ト', 'ナ', 'ニ', 'ヌ', 'ネ', 'ノ', 'ハ', 'ヒ', 'フ',
      'ヘ', 'ホ', 'マ', 'ミ', 'ム', 'メ', 'モ', 'ヤ', 'ユ', 'ヨ', 'ラ', 'リ', 'ル',
      'レ', 'ロ', 'ワ', 'ン', '゛', '゜' };

    private static char HANKAKU_KATAKANA_FIRST_CHAR = HANKAKU_KATAKANA[0];
    private static char HANKAKU_KATAKANA_LAST_CHAR = HANKAKU_KATAKANA[HANKAKU_KATAKANA.Length - 1];
    public static char hankakuKatakanaToZenkakuKatakana(char c)
    {
        if (c >= HANKAKU_KATAKANA_FIRST_CHAR && c <= HANKAKU_KATAKANA_LAST_CHAR)
        {
            return ZENKAKU_KATAKANA[c - HANKAKU_KATAKANA_FIRST_CHAR];
        }
        else
        {
            return c;
        }
    }
    public static char mergeChar(char c1, char c2)
    {
        if (c2 == 'ﾞ')
        {
            if ("ｶｷｸｹｺｻｼｽｾｿﾀﾁﾂﾃﾄﾊﾋﾌﾍﾎ".IndexOf(c1) > 0)
            {
                switch (c1)
                {
                    case 'ｶ': return 'ガ';
                    case 'ｷ': return 'ギ';
                    case 'ｸ': return 'グ';
                    case 'ｹ': return 'ゲ';
                    case 'ｺ': return 'ゴ';
                    case 'ｻ': return 'ザ';
                    case 'ｼ': return 'ジ';
                    case 'ｽ': return 'ズ';
                    case 'ｾ': return 'ゼ';
                    case 'ｿ': return 'ゾ';
                    case 'ﾀ': return 'ダ';
                    case 'ﾁ': return 'ヂ';
                    case 'ﾂ': return 'ヅ';
                    case 'ﾃ': return 'デ';
                    case 'ﾄ': return 'ド';
                    case 'ﾊ': return 'バ';
                    case 'ﾋ': return 'ビ';
                    case 'ﾌ': return 'ブ';
                    case 'ﾍ': return 'ベ';
                    case 'ﾎ': return 'ボ';
                }
            }
        }
        else if (c2 == 'ﾟ')
        {
            if ("ﾊﾋﾌﾍﾎ".IndexOf(c1) > 0)
            {
                switch (c1)
                {
                    case 'ﾊ': return 'パ';
                    case 'ﾋ': return 'ピ';
                    case 'ﾌ': return 'プ';
                    case 'ﾍ': return 'ペ';
                    case 'ﾎ': return 'ポ';
                }
            }
        }
        return c1;
    }
    public static String hankakuKatakanaToZenkakuKatakana(String s)
    {
        if (s.Length == 0)
        {
            return s;
        }
        else if (s.Length == 1)
        {
            return hankakuKatakanaToZenkakuKatakana(s[0]) + "";
        }
        else
        {
            StringBuilder sb = new StringBuilder(s);
            int i = 0;
            for (i = 0; i < sb.Length - 1; i++)
            {
                char originalChar1 = sb[i];
                char originalChar2 = sb[i + 1];
                char margedChar = mergeChar(originalChar1, originalChar2);
                if (margedChar != originalChar1)
                {
                    sb[i] = margedChar;
                    sb.Remove(i + 1,1);
                }
                else
                {
                    char convertedChar = hankakuKatakanaToZenkakuKatakana(originalChar1);
                if (convertedChar != originalChar1)
                {
                    sb[i] = convertedChar;
                }
                }
            }
            if (i < sb.Length)
            {
                char originalChar1 = sb[i];
                char convertedChar = hankakuKatakanaToZenkakuKatakana(originalChar1);
                if (convertedChar != originalChar1)
                {
                    sb[i] = convertedChar;
                }
            }
            return sb.ToString();
        }

    }

```