package com.lhtools.ui.fragment.settings;

import android.content.SharedPreferences;
import android.os.Bundle;
import android.text.Editable;
import android.text.TextUtils;
import android.text.TextWatcher;
import android.view.LayoutInflater;
import android.view.View;
import android.view.ViewGroup;
import android.widget.Button;
import android.widget.EditText;
import android.widget.ImageView;
import android.widget.LinearLayout;
import android.widget.TextView;
import android.widget.Toast;

import com.lhtools.R;
import com.lhtools.manager.alarm.AlalarmManager;
import com.lhtools.ui.activity.SettingsFragmentActivity;
import com.lhtools.ui.fragment.BaseFragment;
import com.lhtools.util.tool.NormalUtil;

import java.text.DecimalFormat;

/**
 * Created by rnd on 2017/10/19.
 */

public class AlarmFragment extends BaseFragment{
    private View view;
    private EditText highalarmtemp;
    private EditText lowalarmtemp;
    private TextView txthighalarm;
    private TextView txtlowalarm;
    private ImageView highswitchimg;
    private ImageView lowswitchimg;
    private LinearLayout llhighswitch,lllowswitch;
    private Button btnsave,btncancel;
    private SettingsFragmentActivity sfa;

    private AlalarmManager alalarmManager;

    //设备地址
    private String deviceAddre;

    //获取当前的是否为华氏度和摄氏度
    private String currentSymbol;

    /**
     * 获取是否为c或者f的之间的转换
     */
    private static final String PREFS_SET_CORF="SetCORF";

    //存储度数和华氏度
    private SharedPreferences spsetcorf;

    /**
     * 获取设备的地址
     */
    private static final String PREFS_SET_ADDRE = "SetAddre";

    private SharedPreferences spsetaddre;

    /**
     * 获取设备的开发状态 高温
     */
    private static final String PREFS_SET_HIGHALARM_SWITCH = "SetHighSwitch";

    private SharedPreferences spsethighswitch;

    /**
     * 获取设备的开发状态 低温
     */
    private static final String PREFS_SET_LOWALARM_SWITCH = "SetLowSwitch";

    private SharedPreferences spsetlowswitch;

    private static final String PREFS_SET_HIGHALARM = "SetHighAlarm";

    private SharedPreferences spsethighalarm;

    private static final String PREFS_SET_LOWALARM = "SetLowAlarm";

    private SharedPreferences spsetlowalarm;


    @Override
    public View onCreateView(LayoutInflater inflater, ViewGroup container, Bundle savedInstanceState) {

        view = inflater.inflate(R.layout.alarm_fg_layout, container,false);
        sfa = (SettingsFragmentActivity)getActivity();

        initView(view);
        initEvent();

        initData();

        return view;

    }

    /**
     * 获取当前的数据
     */
    private void initData() {
        deviceAddre = alalarmManager.getDeviceAddre(spsetaddre);
        currentSymbol = alalarmManager.getCORF(spsetcorf,deviceAddre);
        //首先是开关开启状态
        if(alalarmManager.getHighAlarmSwitch(spsethighswitch,deviceAddre)){
            //开启的时候
            highswitchimg.setImageResource(R.drawable.switchonk);
        }else{
            //关闭的时候
            highswitchimg.setImageResource(R.drawable.switchoffk);
        }

        if(alalarmManager.getLowAlarmSwitch(spsetlowswitch,deviceAddre)){
            lowswitchimg.setImageResource(R.drawable.switchonk);
        }else{
            lowswitchimg.setImageResource(R.drawable.switchoffk);
        }

        //获取温度单位
        if(currentSymbol.equals("C")){
            txthighalarm.setText(sfa.getResources().getText(R.string.tt_celcius));
            txtlowalarm.setText(sfa.getResources().getText(R.string.tt_celcius));
            String high= alalarmManager.getTempHighAlarm(spsethighalarm,deviceAddre);
            highalarmtemp.setText(high);
            String low= alalarmManager.getTempLowAlarm(spsetlowalarm,deviceAddre);
            lowalarmtemp.setText(low);

        }else{
            txthighalarm.setText(sfa.getResources().getText(R.string.tt_fahren_heit));
            txtlowalarm.setText(sfa.getResources().getText(R.string.tt_fahren_heit));
            String high = alalarmManager.getTempHighAlarm(spsethighalarm,deviceAddre);
            String low= alalarmManager.getTempLowAlarm(spsetlowalarm,deviceAddre);
            float h = Float.parseFloat(high);
            float l = Float.parseFloat(low);
            DecimalFormat df = new DecimalFormat("1");//乘以100后以百分比形式输出,此处输出"12%"
            String newh = df.format(NormalUtil.ConvertCelciusToFahren(h));
            String newl = df.format(NormalUtil.ConvertCelciusToFahren(l));
            highalarmtemp.setText(newh);
            lowalarmtemp.setText(newl);

        }

    }

    /**
     * 初始化控件
     * @param view
     */
    private void initView(View view) {
        alalarmManager = new AlalarmManager(sfa);

        //摄氏度和华氏度之间的转换
        spsetcorf =  sfa.getSharedPreferences(PREFS_SET_CORF, 0);
        //存储设备地址
        spsetaddre = sfa.getSharedPreferences(PREFS_SET_ADDRE,0);
        //高温开关
        spsethighswitch = sfa.getSharedPreferences(PREFS_SET_HIGHALARM_SWITCH,0);
        //低温开关
        spsetlowswitch = sfa.getSharedPreferences(PREFS_SET_LOWALARM_SWITCH,0);
        //高温温度数据
        spsethighalarm = sfa.getSharedPreferences(PREFS_SET_HIGHALARM,0);
        //低温数据
        spsetlowalarm = sfa.getSharedPreferences(PREFS_SET_LOWALARM,0);

        highalarmtemp = (EditText) view.findViewById(R.id.highalarmtemp);
        lowalarmtemp = (EditText) view.findViewById(R.id.lowalarmtemp);

        txthighalarm = (TextView) view.findViewById(R.id.txthighcorf);
        txtlowalarm = (TextView) view.findViewById(R.id.txtlowcorf);

        highswitchimg = (ImageView) view.findViewById(R.id.highswitchimg);
        lowswitchimg = (ImageView) view.findViewById(R.id.lowswitchimg);

        llhighswitch = (LinearLayout) view.findViewById(R.id.ll_highswitch);
        lllowswitch = (LinearLayout) view.findViewById(R.id.ll_lowswitch);

        btnsave = (Button) view.findViewById(R.id.btnsave);
        btncancel = (Button) view.findViewById(R.id.btncancel);
    }

    private void initEvent(){
        llhighswitch.setOnClickListener(this);
        lllowswitch.setOnClickListener(this);
        btnsave.setOnClickListener(this);
        btncancel.setOnClickListener(this);

        highalarmtemp.addTextChangedListener(hightextWatcher);
        lowalarmtemp.addTextChangedListener(lowtxtWatcher);
    }

    /**
     * 新增加输入文本框 第一位不能为零 不能大于3位数
     * 更新于 2017／5／19 8：07
     */
    private TextWatcher hightextWatcher = new TextWatcher() {
        @Override
        public void beforeTextChanged(CharSequence s, int start, int count, int after) {

        }

        @Override
        public void onTextChanged(CharSequence s, int start, int before, int count) {
            String str = highalarmtemp.getText().toString().trim();
            if(str.indexOf("0")==0){
                highalarmtemp.setText("");
                Toast.makeText(sfa, "首位输入不能为0", Toast.LENGTH_SHORT).show();
            }else if(str.length()>3){
                highalarmtemp.setText("");
                Toast.makeText(sfa, "输入不能超过3位数", Toast.LENGTH_SHORT).show();
            }
        }
        @Override
        public void afterTextChanged(Editable s) {

        }
    };

    private TextWatcher lowtxtWatcher = new TextWatcher() {
        @Override
        public void beforeTextChanged(CharSequence s, int start, int count, int after) {

        }
        @Override
        public void onTextChanged(CharSequence s, int start, int before, int count) {
            String str = lowalarmtemp.getText().toString().trim();
            if(str.indexOf("0")==0){
                lowalarmtemp.setText("");
                Toast.makeText(sfa, "首位不能输入为0", Toast.LENGTH_SHORT).show();
            }else if(str.length()>3){
                lowalarmtemp.setText("");
                Toast.makeText(sfa, "输入不能超过3位数", Toast.LENGTH_SHORT).show();
            }
        }

        @Override
        public void afterTextChanged(Editable s) {

        }
    };


    @Override
    public void onClick(View v) {
        switch (v.getId()){
            case R.id.ll_highswitch:
                if(highalarmtemp.getText().toString().isEmpty()){
                    Toast.makeText(sfa,"Input cannot be empty",Toast.LENGTH_SHORT).show();
                    return;
                }else{
                    if(alalarmManager.getHighAlarmSwitch(spsethighswitch,deviceAddre)){
                        alalarmManager.saveHighAlarmSwitch(spsethighswitch,deviceAddre,false);
                        highswitchimg.setImageResource(R.drawable.switchoffk);
                    }else{
                        alalarmManager.saveHighAlarmSwitch(spsethighswitch,deviceAddre,true);
                        highswitchimg.setImageResource(R.drawable.switchonk);
                    }
                }

                break;

            case R.id.ll_lowswitch:
                if(lowalarmtemp.getText().toString().isEmpty()){
                    Toast.makeText(sfa,"Input cannot be empty",Toast.LENGTH_SHORT).show();
                    return;
                }else{
                    if(alalarmManager.getLowAlarmSwitch(spsetlowswitch,deviceAddre)){
                        alalarmManager.saveLowAlarmSwitch(spsetlowswitch,deviceAddre,false);
                        lowswitchimg.setImageResource(R.drawable.switchoffk);
                    }else{
                        alalarmManager.saveLowAlarmSwitch(spsetlowswitch,deviceAddre,true);
                        lowswitchimg.setImageResource(R.drawable.switchonk);
                    }
                }

                break;
            //存储到高温或者低温里面去
            case R.id.btnsave:
                getHighOrLowAlarm();

                break;
            case R.id.btncancel:
                sfa.finish();
                break;
        }
    }

    /**
     * 获取高温和低温报警值
     */
    private void getHighOrLowAlarm(){
        String h = highalarmtemp.getText().toString().trim();
        String l = lowalarmtemp.getText().toString().trim();
        //检查开关
        if(isTempCheck()) {
            //如果高温开关打开的时候
            if (alalarmManager.getHighAlarmSwitch(spsethighswitch, deviceAddre)) {
                if (!TextUtils.isEmpty(h)) {
                    if (currentSymbol.equals("C")) {
                        alalarmManager.saveTempHighAlarm(spsethighalarm,deviceAddre,h);  //存入高低温
                    } else { //如果是华氏度就要进去处理
                        float hfs = Float.parseFloat(h);
                        float hf = NormalUtil.convertFahrenheitToCelcius(hfs);
                        alalarmManager.saveTempHighAlarm(spsethighalarm,deviceAddre,hf+"");
                    }
                }
            }

            //如果是低温的时候
            if (alalarmManager.getLowAlarmSwitch(spsetlowswitch, deviceAddre)) {
                if (!TextUtils.isEmpty(l)) {
                    if (currentSymbol.equals("C")) {
                        alalarmManager.saveTempLowAlarm(spsetlowalarm,deviceAddre,l);  //存入高低温
                    } else { //如果是华氏度就要进去处理
                        float lfs = Float.parseFloat(l);
                        float lf = NormalUtil.convertFahrenheitToCelcius(lfs);
                        alalarmManager.saveTempLowAlarm(spsetlowalarm,deviceAddre,lf+"");
                    }
                }
            }
        }
    }

    /**
     * 检查是否正确
     * @return
     */
    private boolean isTempCheck(){
        String a = highalarmtemp.getText().toString().trim();
        String b = lowalarmtemp.getText().toString().trim();
        if (currentSymbol.equals("C") && !currentSymbol.isEmpty()) {
            float aa=299,bb=-49;
            if(!a.equals("")&&!a.equals(null)) {
                aa= Float.parseFloat(a);
            }
            if(!b.equals("")&&!b.equals(null)) {
                bb = Float.parseFloat(b);
            }
            if (aa >= 300.0) {
                Toast.makeText(getActivity(), "Can only be set for -50°C to 300°C or -58°F to 572°F!", Toast.LENGTH_SHORT).show();
                return false;
            } else if (bb <= -50.0) {
                Toast.makeText(getActivity(), "Can only be set for -50°C to 300°C or -58°F to 572°F!", Toast.LENGTH_SHORT).show();
                return false;
            } else if (aa <= -50.0) {
                Toast.makeText(getActivity(), "Can only be set for -50°C to 300°C or -58°F to 572°F!", Toast.LENGTH_SHORT).show();
                return false;
            } else if (bb >= 300.0) {
                Toast.makeText(getActivity(), "Can only be set for -50°C to 300°C or -58°F to 572°F!", Toast.LENGTH_SHORT).show();
                return false;
            }else if(aa<=bb){
                Toast.makeText(getActivity(), "High temperature alarm value must be greater than low temperature alarm value", Toast.LENGTH_SHORT).show();
                return false;
            }

        } else if (currentSymbol.equals("C") && !currentSymbol.isEmpty()) {
            float aa=571,bb=-57;
            if(!a.equals("")&&!a.equals(null)) {
                aa= Float.parseFloat(a);
            }
            if(!b.equals("")&&!b.equals(null)) {
                bb = Float.parseFloat(b);
            }

            if (aa >= 572.0) {
                Toast.makeText(getActivity(), "Can only be set for -50°C to 300°C or -58°F to 572°F!", Toast.LENGTH_SHORT).show();
                return false;
            } else if (bb <= -58.0) {
                Toast.makeText(getActivity(), "Can only be set for -50°C to 300°C or -58°F to 572°F!", Toast.LENGTH_SHORT).show();
                return false;
            } else if (aa <= -58.0) {
                Toast.makeText(getActivity(), "Can only be set for -50°C to 300°C or -58°F to 572°F!", Toast.LENGTH_SHORT).show();
                return false;
            } else if (bb >= 572.0) {
                Toast.makeText(getActivity(), "Can only be set for -50°C to 300°C or -58°F to 572°F!", Toast.LENGTH_SHORT).show();
                return false;
            }else if(aa<=bb){
                Toast.makeText(getActivity(), "High temperature alarm value must be greater than low temperature alarm value", Toast.LENGTH_SHORT).show();
                return false;
            }
        }

        return true;
    }
}
