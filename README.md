1. Layout (xml files)
Апликацијата претставува квиз составен од пет прашања со по три
одговори. Таа содржи база на податоци, која ги чува прашањата и преку
истата се менуваат. Апликацијата води евиденција за поените на корисникот
и на крајот на квизот ги прикажува вкупните освоени поени.
Апликацијата е насловена Quiz. Составена е од две главни дејности
(activities) – Main Activity и Result Activity. Main Activity ги врти и прикажува
прашањата и одговорите а Result Activity го прикажува само резултатот.
Main Activity во својот layout содржи еден наслов, TextView со текстот
на прашањето, RadioGroup со три RadioButtons кои ги прикажуваат
понудените одговори и еден Button за кликање на следното прашање (Next).
ResultActivity е составен само од еден TextView кој го дава резултатот
на крај.
И двете имаат LinearLayout вграден во RelativeLayout.

2. MainActivity (Code)
public class MainActivity extends AppCompatActivity {
List&lt;Question&gt; questList;
int score = 0;
int questid = 0;
Question currQuest;
TextView textQuestion;
RadioButton r1, r2, r3;
Button nxt;
@Override
protected void onCreate(Bundle savedInstanceState) {
super.onCreate(savedInstanceState);
setContentView(R.layout.activity_main);
DbHelper db = new DbHelper(this);
questList = db.getAllQuestions();
currQuest = questList.get(questid);

nxt = (Button) findViewById(R.id.button1);
nxt.setOnClickListener(new View.OnClickListener() {
@Override
public void onClick(View v) {
RadioGroup group = (RadioGroup) findViewById(R.id.radiogroup);
RadioButton answer = (RadioButton)
findViewById(group.getCheckedRadioButtonId());
group.clearCheck();
Log.d(&quot;youranswer&quot;, currQuest.getANSWER() + &quot; &quot; +
answer.getText());
if (currQuest.getANSWER().equals(answer.getText())) {
score++;
Log.d(&quot;score&quot;, &quot;Your score&quot; + score);
}
if (questid &lt; 5) {
currQuest = questList.get(questid);
setQuestionView();
} else {
Intent intent = new Intent(MainActivity.this,
ResultActivity.class);
Bundle b = new Bundle();
b.putInt(&quot;score&quot;, score); //Your Score
intent.putExtras(b); // Put your score to your next intent
startActivity(intent);
finish();
}

}
});
}

private void setQuestionView()
{
textQuestion.setText(currQuest.getQUESTION());
r1.setText(currQuest.getOPTA());
r2.setText(currQuest.getOPTB());
r3.setText(currQuest.getOPTC());
questid++;
}

3.DbHelper
Креирањето на дата базата, односно оперирањето со неа е извршено преку
помошната класа SQLiteOpenHelper

public class DbHelper extends SQLiteOpenHelper {
private static final int DATABASE_VERSION=1;
private static final String DATABASE_NAME=&quot;GeneralKnowledgeQuiz&quot;;
Question question; (објект од класата Questions)
private SQLiteDatabase dbase;
public DbHelper(Context context){
super(context,DATABASE_NAME,null,DATABASE_VERSION); }
@Override
public void onCreate(SQLiteDatabase db){
dbase=db;
String sql = &quot;CREATE TABLE IF NOT EXISTS&quot; + TABLE_QUEST + &quot; ( &quot;
+ KEY_ID + &quot;INTEGER PRIMARY KEY AUTOINCREMENT, &quot; + KEY_QUES
+ &quot; TEXT, &quot; + KEY_ANSWER + &quot; TEXT, &quot; + KEY_OPTA + &quot; TEXT, &quot;
+ KEY_OPTB + &quot; TEXT, &quot; + KEY_OPTC + &quot; TEXT) &quot;;
db.execSQL(sql);
addQuestions();
}

-Функцијата addQuestions служи за вметнување на податоците во објекти од
класата Questions. Уште една функција addQuestions ги зема истите објекти
како параметар и преку нив DbHelper ја пополнува табелата со податоците

private void addQuestions()
{
Question q1=new Question(0,&quot;What is the name of the animal that changes
color?&quot;,&quot;Chameleon&quot;,&quot;Otter&quot;,&quot;Mink&quot;,&quot;Chameleon&quot;);
this.addQuestion(q1);
Question q2=new Question(1,&quot;What horoscope sign comes between Leo and
Libra&quot;,&quot;Taurus&quot;,&quot;Pisces&quot;,&quot;Virgo&quot;,&quot;Virgo&quot;);
this.addQuestion(q2);
Question q3=new Question(2,&quot;What does RAM stand for in computer
technology&quot;,&quot;Read Only Memory&quot;,&quot;Random Access Memory&quot;,&quot;Solid State
Drive&quot;,&quot;Random Access Memory&quot;);
this.addQuestion(q3);
Question q4=new Question(3,&quot;What is out Galaxy called?&quot;,&quot;The Milky
Way&quot;,&quot;Andromeda&quot;,&quot;Spiral&quot;, &quot;The Milky Way&quot;);
this.addQuestion(q4);
Question q5=new Question(4,&quot;How many teeth do humans have&quot;,&quot;Twenty-
eight&quot;,&quot;Thirty-two&quot;,&quot;Forty-eight&quot;,&quot;Thirty-two&quot;);

this.addQuestion(q5);
}

@Override
public void onUpgrade(SQLiteDatabase db,int oldV, int newV) {
//Drop older table if existed
db.execSQL( &quot;DROP TABLE IF EXISTS&quot; + TABLE_QUEST);
//Create tables again
onCreate(db);
}

3. QuizContract
import android.provider.BaseColumns;
public class QuizContract {
public static class GeneralKnowledge implements BaseColumns {
public static final String TABLE_QUEST=&quot;quest&quot;;
public static final String KEY_ID = &quot;id&quot;;
public static final String KEY_QUES=&quot;question&quot;;
public static final String KEY_ANSWER=&quot;answer&quot;;
public static final String KEY_OPTA=&quot;opt a&quot;;
public static final String KEY_OPTB=&quot;opt b&quot;;
public static final String KEY_OPTC=&quot;opt c&quot;;
}
}
