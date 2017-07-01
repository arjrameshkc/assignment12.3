# assignment12.3


import java.io.IOException;
import org.apache.hadoop.fs.Path;
import org.apache.hadoop.conf.*;
import org.apache.hadoop.io.*;
import org.apache.hadoop.mapreduce.*;
import org.apache.hadoop.mapreduce.lib.input.FileInputFormat;
import org.apache.hadoop.mapreduce.lib.input.TextInputFormat;
import org.apache.hadoop.mapreduce.lib.output.FileOutputFormat;
import org.apache.hadoop.mapreduce.lib.output.TextOutputFormat;
public class Hospital_Answer {
public static class Map extends Mapper<LongWritable, Text, Text,
IntWritable> {
private Text hospital = new Text();
private IntWritable percent=new IntWritable();
public void map(LongWritable key, Text value, Context context )
throws IOException, InterruptedException {
String line = value.toString();
String str[]=line.split(",");
if(str.length==4){
hospital.set(str[0]);
if(str[3].matches("\\d+")){
int i=Integer.parseInt(str[3]);
percent.set(i);
}
context.write(hospital,percent);
}
}
}
public static class Reduce extends Reducer<Text, IntWritable,
Text, IntWritable> {
public void reduce(Text key, Iterable<IntWritable> values,
Context context)
throws IOException, InterruptedException {
int sum = 0;
int l=0;
for (IntWritable val : values) {
l+=1;
sum += val.get();
}
sum=sum/l;
context.write(key, new IntWritable(sum));
}
}
public static void main(String[] args) throws Exception {
Configuration conf = new Configuration();
@SuppressWarnings("deprecation")
Job job = new Job(conf, "hospitalanswer");
job.setJarByClass(Hospital_Answer.class);
job.setMapOutputKeyClass(Text.class);
job.setMapOutputValueClass(IntWritable.class);
job.setOutputKeyClass(Text.class);
job.setOutputValueClass(IntWritable.class);
job.setMapperClass(Map.class);
job.setReducerClass(Reduce.class);
job.setInputFormatClass(TextInputFormat.class);
job.setOutputFormatClass(TextOutputFormat.class);
FileInputFormat.addInputPath(job, new Path(args[0]));
FileOutputFormat.setOutputPath(job, new Path(args[1]));
Path out=new Path(args[1]);
out.getFileSystem(conf).delete(out);
job.waitForCompletion(true);
}
}

Assignment12.3.2

import org.apache.hadoop.fs.Path;
import org.apache.hadoop.conf.*;
import org.apache.hadoop.io.*;
import org.apache.hadoop.mapreduce.*;
import org.apache.hadoop.mapreduce.lib.input.FileInputFormat;
import org.apache.hadoop.mapreduce.lib.input.TextInputFormat;
import org.apache.hadoop.mapreduce.lib.output.FileOutputFormat;
import org.apache.hadoop.mapreduce.lib.output.TextOutputFormat;
public class Most_Popularques {
public static class Map extends Mapper<LongWritable, Text, Text,
IntWritable> {
private Text question = new Text();
private static final IntWritable one = new IntWritable(1);
public void map(LongWritable key, Text value, Context context )
throws IOException, InterruptedException {
String line = value.toString();
String str[]=line.split(",");
question.set(str[1]);
context.write(question, one);
}}
public static class Reduce extends Reducer<Text, IntWritable,
Text, IntWritable> {
public void reduce(Text key, Iterable<IntWritable> values,
Context context)
throws IOException, InterruptedException {
int sum = 0;
for (IntWritable val : values) {
sum += val.get();
}
context.write(key, new IntWritable(sum));
}
}
public static void main(String[] args) throws Exception {
Configuration conf = new Configuration();
@SuppressWarnings("deprecation")
Job job = new Job(conf, "wordcount");
job.setJarByClass(Most_Popularques.class);
job.setMapOutputKeyClass(Text.class);
job.setMapOutputValueClass(IntWritable.class);
job.setOutputKeyClass(Text.class);
job.setOutputValueClass(IntWritable.class);
job.setMapperClass(Map.class);
job.setReducerClass(Reduce.class);
job.setInputFormatClass(TextInputFormat.class);
job.setOutputFormatClass(TextOutputFormat.class);
FileInputFormat.addInputPath(job, new Path(args[0]));
FileOutputFormat.setOutputPath(job, new Path(args[1]));
Path out=new Path(args[1]);
out.getFileSystem(conf).delete(out);
job.waitForCompletion(true);
}
}
