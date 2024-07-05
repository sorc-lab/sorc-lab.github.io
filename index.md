![Sorc Lab](/SorcLabLogo_White.png)

# Disk Utils
- [Cloning Disks](/blog/cloning-disks.md)
- [DD Copy](/blog/dd-copy.md)

<div class="status-box">
    <p>STATUS BOX</p>
</div>

<script>
    document.addEventListener("DOMContentLoaded", function() {
        var statusBox = document.querySelector(".status-box");
        //console.log(statusBox);

        var statusBox = document.querySelector(".status-box");
        //console.log(statusBox);

        //var numbers = [58, 75, 45, 0, 55, 36, 73, 55, 27, 73, 45, 64, 73, 45, 91, 45, 18, 27];
        //var average = calculateAverage(numbers);

        //statusBox.textContent = "MAINTENANCE MODE, SYSTEM: " + average + "%";
        //statusBox.innerHTML = "MAINTENANCE MODE<br>SYSTEM: " + 36.19 + "%";
        statusBox.innerHTML = "SYSTEM: " + 40.21 + "%";
    });
    

    function calculateAverage(numbers) {
        if (numbers.length === 0) {
            return 0;
        }
        var sum = 0;
        for (var i = 0; i < numbers.length; i++) {
            sum += numbers[i];
        }
        var average = sum / numbers.length;
        //return average;
        return parseFloat(average.toFixed(2));
    }

    // setTimeout(function() {
    //     updateStatusBox("New content.");
    // }, 3000);
</script>

<!-- BK
![Sorc Lab](/SorcLabLogo_White.png)

# Disk Utils
- [Cloning Disks](/blog/cloning-disks.md)
- [DD Copy](/blog/dd-copy.md)

-->
