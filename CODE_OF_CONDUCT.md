import org.bytedeco.ffmpeg.global.avcodec;
import org.bytedeco.ffmpeg.global.avutil;
import org.bytedeco.ffmpeg.global.swscale;
import org.bytedeco.javacv.*;

import java.io.File;

public class CreateVideo {
    public static void main(String[] args) {
        String outputFile = "output.mp4"; // Output video file
        int width = 640, height = 480; // Video resolution
        int frameRate = 30; // Frames per second
        int duration = 5; // Video duration in seconds

        try {
            // Create FFmpeg frame recorder
            FFmpegFrameRecorder recorder = new FFmpegFrameRecorder(outputFile, width, height);
            recorder.setVideoCodec(avcodec.AV_CODEC_ID_H264); // Use H.264 codec
            recorder.setFormat("mp4"); // Output format
            recorder.setFrameRate(frameRate);
            recorder.setPixelFormat(avutil.AV_PIX_FMT_YUV420P);

            recorder.start(); // Start recording

            // Create a Java2D frame converter
            OpenCVFrameConverter.ToMat converter = new OpenCVFrameConverter.ToMat();
            Frame frame;

            for (int i = 0; i < frameRate * duration; i++) {
                // Generate a simple color frame (red background)
                org.bytedeco.opencv.opencv_core.Mat mat = new org.bytedeco.opencv.opencv_core.Mat(height, width, org.bytedeco.opencv.opencv_core.CV_8UC3);
                org.bytedeco.opencv.opencv_core.Scalar color = new org.bytedeco.opencv.opencv_core.Scalar(0, 0, 255, 0);
                org.bytedeco.opencv.global.opencv_core.rectangle(mat, new org.bytedeco.opencv.opencv_core.Rect(50, 50, 200, 200), color, -1, 8, 0);

                frame = converter.convert(mat);
                recorder.record(frame); // Add frame to video
            }

            recorder.stop(); // Stop recording
            recorder.release(); // Release resources
            System.out.println("Video created successfully: " + outputFile);

        } catch (Exception e) {
            e.printStackTrace();
        }
    }
}
