#דוח סיכום פרויקט – Deep Learning  - Recurrent Neural Network
## מגישות רז שיין 201054103 , ליבנת גרשוני 301792792 ##

בפרויקט זה התמודדנו עם חילוץ מידע מרצפים של טקסט, בעזרתו יצרנו מודל והצלחנו לחולל רצפים חדשים.
ה-Data Set איתו בחרנו לעבוד היה רשימה ארוכה של ציטוטים מפורסמים שונים. אורך Data Set הינו 2515 רשומות, כאשר כל רשומה מכילה ציטוט באורך שונה. את סט הנתונים הורדנו מהאינטרנט כקובץ .sql והמרנו אותו לפורמט csv.
בחרנו בסט הנתונים הזה כיוון שמשפטי חוכמה וציטוטים שם דמויות מובילות מהעבר כמו: בודהה, בנג'מין פרנקלין, המינגווי, שייקספיר ועוד. עניין אותנו לבחון עד כמה הרצפים יצאו קרובים למציאות ולמשפטים שסביר להניח היו נאמרים ע"י אותם אנשים.
האתגר העיקרי בעבודה עם סט הנתונים הנ"ל הוא שרצף המילים שנייצר לא יהיה באיכות טובה וכתוצאה מכך לא יהיה שימוש משמעותי במשפטי החוכמה שנייצר, למשפטים אלו אפשר שיהיה שימוש בנאומים עתידיים ועוגיות מזל.


### Data Preprocessing: ###
את הרשומות בפרויקט קראנו רשומה רשומה מקובץ ה-data.csv (נמצא תחת התיקייה data בפרויקט).
בחלק מן הרשומות היו תווים שלא מוכרים לשיטת ה-UTF8 ולכן היה עלינו להחליף תווים אלו בתווים מקבילים המוכרים לשיטה.
כל רשימה שהכילה ציטוט חולקה למספר משפטים במידה והציטוט הכיל מעל למשפט אחד (סה"כ עבדנו עם 3294 משפטים) וכל האותיות הוקטנו (case sensitive). לכל משפט הוספנו SENTENCE_START ו- SENTENCE_END על מנת ללמוד איזה מילים נוטות להופיע בתחילת משפט, ואיזה מילים נוטות להופיע בסיום.
ולבסוף כל משפט עבר tokenizing ובעצם יצרנו רשימה ארוכה של מילים – כ- 6411 מילים שונות.
יש משמעות למספר המופעים של כל מילה, לא נרצה ליצור רצף חדש עם מילה נדירה שהופיעה רק פעם אחת כי רוב הסיכויים שהיא לא תהיה רלוונטית – בעקר כיוון שלא ניתן ללמוד עליה יותר מידיי, לכן, היה צורך בלהגביל את גודל רשימת המילים בה נשתמש ליצירת הרצפים החדשים שלנו. לשם כך הגדרנו רשימה חדשה שנקראת vocabularyList , הרשימה מכילה את 1500 המילים השכיחות ביותר. את כל המילים שלא נכנסו ל- vocabularyList כיוון שהן נדירות יחסית לשאר, נחליף במילה UNKNOWN_TOKEN וכמובן שאם בעתיד המילה UNKNOWN_TOKEN תיבחר כחלק מרצף נדלג עליה.
מתוך הרשימה הזאת ניצור שני מילונים (data matrix) כפי שנלמד בשיעור:  index_to_word ו- word_to_index שני המילונים האלה יעזרו לנו במשך ליצירת המשפטים
לפני הרצה : יש להוריד את הpackage  nltk . ולכתבו בprogram . nltk.download() , רק בפעם הראשונה

### בניית המודל ###
חילקנו את כל המשפטים שלנו (כל ה-3294 ) ל- X_train ול- Y_train מהצורה הבאה:
 X_train:	SENTENCE_START It takes time to build a castle
Y_train:		It takes time to build a castle SENTENCE_END
ולכן עבור המילה הראשונה ב-X המילה הראשונה ב-Y תהיה הבאה בתור.
מהחלוקה הזאת יצרנו מודל Simpel_rnn  - RNN אשר מבוסס על השיטות של Numpy. המודל מקבל כקלט את מספר הקודקודים בשכבה הנסתרת, אנחנו בחרנו ב-100.


לאחר שהמודל בנוי ניתן למדוד את הצלחתו במספר דרכים:
<ol>
<li>יצירת משפטים ובדיקת תקינותם ע"י המשתמש </li>
<li>בחינת ה-LOSS של המודל	</li>
<li>יצירת משפטים חדשים והשוואתם באמצעות דימון למשפטים בסט הנתונים </li>
</ol>

### בחינת ה-LOSS של המודל: ###
על מנת לבחון את ה-LOSS של המודל עלינו תחילה לחשב את ה-LOSS של מודל רנדומלי על מנת שנוכל להשוות את התוצאות, התוצאה שיצאה לנו במודל הרנדומלי הייתה - 7.313220 כעת היה עלינו לחשב את ה-LOSS של המודל שלנו.
את הLOSS מחשבים בעזרת SGD אלגוריתם לאופטימיזציה של תוצאות, האלגוריתם משתמש בגרדיאנט אותו נייצר באמצעות אלגוריתם לפעפוע לאחור המיועד לRNN - Backpropagation Through Time (BPTT) . לבסוף נאמן את אלגוריתם ה-SGD על ה-train set שלנו ונראה שככל שהאלגוריתם רואה יותר דוגמאות הוא לומד יותר וה-LOSS שלנו קטן, כמו כן משתנה מאוד חשוב בחישוב הSGD הוא nepoch שבעצם מסמל את מספר הפעמים שהרשת לומדת , לפרמטר הזה בחרנו 100 איטרציות  – הLOSS לאחר שהאלגוריתם ראה את כל המשפטים ב data set הוא 4.238144 .

### יצירת משפטים: ###
ניצור משפט בעזרת המודל, תחילה נבחר את המילה הראשונה במשפט - SENTENCE_START ונשתמש ב- forward propagation  שזה בעצם חיזוי של ההסתברות של כל מילה להיות המילה הבאה, מתוך זה נבחר את המילה בעלת ההסתברות הגבוהה ביותר. כך בעצם נשרשר מילים חדשות לרצף המשפט שלנו עד שהמילה SENTENCE_END תצא עם ההסתברות הגבוהה ביותר. במידה ובמהלך יצירת המילים תיבחר המילה UNKNOWN_TOKEN או מילים ללא משמעות כמו > \ וכו', נתעלם ונעבור להגרלת מילה חדשה, כמו כן טיפלנו בכינוי השייכות 's וקיצורים כמו 've.
לבסוף יתקבל משפט אותו נקרא ונחליט האם הוא תקין או לא מבחינה תחבירית והגיונית.

### אלגוריתם לדמיון ###
את אלגוריתם הדמיון שלנו ביצענו באמצעות Minhash בשלוב עם Jaccard אשר מחזיר מספר בין 0 ל-1 לגבי הדמיון של משפט למשפט אחר.

### Simple Algorithm: ###
![alt text](https://github.com/razshain/proj/blob/master/simple.PNG "p1")

### לסיכום – האלגוריתם הפשוט ###
לאחר ההרצה של תוכנית ה-python שבנינו עם משתנים שונים כמו מס' הקדקודים בשכבה החבויה וגודל האוצר מילים שלנו, קיבלנו תוצאות שונות אך לא מצאנו תוצאה שהייתה בצורה מובהקת טובה מהאחרות, לאחר מחקר באינטרנט הגענו למסקנה כי אלגוריתם ה-RNN שנלמד בשיעור לא מתוחכם כמו לדוגמא אלגוריתם נוסף המבוסס עליו – LSTM, לכן פנינו ללימוד ובניית אלגוריתם ה-LSTM. תוצאת האלגוריתם הנ"ל היו בהחלט טובות יותר (פה גם היה שימוש בחבילה של theano )

### בניית אלגוריתם חדש המבוסס על LSTM: ###
לאחר שבחנו את תוצאות המודל, והן לא היו כל כך טובות, חזרנו אחורה ומימשנו מודל נוסף LSTM_rnn המבוסס על LSTM וגם הוא מקבל כקלט את מספר הקודקודים בשכבה הנסתרת, אנחנו בחרנו ב-100.
שמות הפונקציות במודל simple ו LSTMזהות, והמטרה של כל אחת מהן גם כן זהה, אך עדיין קיים שוני במימוש.
לאחר שהמודל בנוי ניתן למדוד את הצלחתו במספר דרכים:
<ol>
<li>יצירת משפטים ובדיקת תקינותם ע"י המשתמש </li>
<li>בחינת ה-LOSS של המודל	</li>
<li>יצירת משפטים חדשים והשוואתם באמצעות דימון למשפטים בסט הנתונים </li>
</ol>

### בחינת ה-LOSS של המודל: ###
על מנת לבחון את ה-LOSS של המודל עלינו תחילה לחשב את ה-LOSS של מודל רנדומלי על מנת שנוכל להשוות את התוצאות, התוצאה שיצאה לנו במודל הרנדומלי הייתה - 7.313220 כעת היה עלינו לחשב את ה-LOSS של המודל שלנו.
את הLOSS מחשבים בעזרת SGD אלגוריתם לאופטימיזציה של תוצאות, נאמן את אלגוריתם ה-SGD על ה-train set שלנו ונראה שככל שהאלגוריתם רואה יותר דוגמאות הוא לומד יותר וה-LOSS שלנו קטן, כמו כן משתנה מאוד חשוב בחישוב הSGD הוא nepoch שבעצם מסמל את מספר הפעמים שהרשת לומדת , לפרמטר הזה בחרנו 100 איטרציות  – הLOSS לאחר שהאלגוריתם ראה את כל המשפטים ב data set הוא0.403794 .

### יצירת משפטים: ###
ניצור משפט בעזרת המודל, תחילה נבחר את המילה הראשונה במשפט - SENTENCE_START ונשתמש ב- forward propagation  שזה בעצם חיזוי של ההסתברות של כל מילה להיות המילה הבאה, מתוך זה נבחר את המילה בעלת ההסתברות הגבוהה ביותר. כך בעצם נשרשר מילים חדשות לרצף המשפט שלנו עד שהמילה SENTENCE_END תצא עם ההסתברות הגבוהה ביותר. במידה ובמהלך יצירת המילים תיבחר המילה UNKNOWN_TOKEN או מילים ללא משמעות כמו > \ וכו', נתעלם ונעבור להגרלת מילה חדשה, כמו כן טיפלנו בכינוי השייכות 's וקיצורים כמו 've.
לבסוף יתקבל משפט אותו נקרא ונחליט האם הוא תקין או לא מבחינה תחבירית והגיונית.

### אלגוריתם לדמיון ###
את אלגוריתם הדמיון שלנו ביצענו באמצעות Minhash (min-wise independent permutations) בשלוב עם Jaccard אשר מחזיר מספר בין 0 ל-1 לגבי הדמיון של משפט למשפט אחר.

### LSTM Algorithm: ###
![alt text](https://github.com/razshain/proj/blob/master/lstm.PNG "p2")

### לסיכום – אלגוריתם LSTM ###
לאחר ההרצה של תוכנית ה-python שבנינו עם משתנים שונים כמו מס' הקדקודים בשכבה החבויה וגודל האוצר מילים שלנו,  תוצאות האלגוריתם הנ"ל היו בהחלט טובות יותר (פה גם היה שימוש בחבילה של theano ), המשפטים נראו הגיוניים יותר, וגם הדמיון לסט הנתונים שלנו היה גדול יותר. לכן האלגוריתם המועדף עלנו הינו LSTM.

