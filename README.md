# CorruptedJarsDetector
Detect/Delete/Repair Corrupted Maven/Gradle Jars on the Fly .

This project came out of pure [frustration](https://stackoverflow.com/questions/52741518/avoid-corrupted-jars-invalid-loc-header-when-using-maven/52752213?noredirect=1#comment92428877_52752213) of manually finding and deleting corrupted jars on Java Projects 

# Design in process

# Some code for now ( will add JavaFX UI )

``` JAVA
import java.io.*;
import java.nio.file.*;
import java.util.*;
import java.util.jar.*;
import java.util.stream.*;

public class JarValidator {

    public static void main(String[] args) throws IOException {
        Path repositoryPath = Path.of("C:\\Users\\johndoe\\.m2");

        if (Files.exists(repositoryPath)) {
            JarValidator jv = new JarValidator();
            List<String> jarReport = new ArrayList<>();
            jarReport.add("Repository to process: " + repositoryPath.toString());
            List<Path> jarFiles = jv.getFiles(repositoryPath, ".jar");
            jarReport.add("Number of jars to process: " + jarFiles.size());
            jarReport.addAll(jv.openJars(jarFiles));
            jarReport.stream().forEach(System.out::println);
        } else {
            System.out.println("Repository path " + repositoryPath + " does not exist.");
        }
    }

    private List<Path> getFiles(Path filePath, String fileExtension) throws IOException {
        return Files.walk(filePath)
                .filter(p -> p.toString().endsWith(fileExtension))
                .collect(Collectors.toList());
    }

    private List<String> openJars(List<Path> jarFiles) {
        int badJars = 0;
        List<String> messages = new ArrayList<>();

        for (Path path : jarFiles) {
            try {
                new JarFile(path.toFile()); // Just dheck for an exception on instantiation.
            } catch (IOException ex) {
                messages.add(path.toAbsolutePath() + " threw exception: " + ex.toString());
                badJars++;
            }
        }
        messages.add("Total bad jars = " + badJars);
        return messages;
    }
}
```

>Output

``` JAVA
run:
Repository to process: C:\Users\johndoe\.m2
Number of jars to process: 4920
C:\Users\johndoe\.m2\repository\bouncycastle\isoparser-1.1.18.jar threw exception: java.util.zip.ZipException: zip END header not found
Total bad jars = 1
BUILD SUCCESSFUL (total time: 2 seconds)

```
