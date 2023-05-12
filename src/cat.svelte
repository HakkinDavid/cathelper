<script context="module">
    import catlogo from './assets/CATHelper.png';
    import sleepingcat from './assets/CATHelperZZZ.png';
    import pinchingcat from './assets/CATHelperPinch.png';
    import crane from './assets/crane.png';

    import katex from "katex";
    
    class Cat {
        log (message = "meow", origin = "🐱") {
            console.log (origin + ": " + message);
        };
        title = "CAT HELPER";
        logo = {
            worktime: catlogo,
            zzz: sleepingcat,
            pinch: pinchingcat
        };
        formula (formula = "") {
            return katex.renderToString(formula, { displayMode: false, throwOnError: false });
        };
        crane = crane;
    };

    class Problem {
        mass = 0;
        P = {
            length: 0,
            angle: 0,
            x: 0, y: 0
        };
        Wvector = 0;
        Momentum = 0;        
        cable = {
            tension: 0,
            ctension: 0,
            angle: {
                magnitude: 0, 
                betweenP: false
            }
        };

        calcWeight () {
            this.Wvector = ((-1)*(this.mass * 9.81));
        };
        calcP () {
            this.P.x = this.P.length*Math.cos(this.P.angle*(Math.PI/180));
            this.P.y = this.P.length*Math.sin(this.P.angle*(Math.PI/180));
        };
        calcMomentum () {
            this.Momentum = this.P.x*this.Wvector;
        };
        calcTension () {
            this.cable.tension = 
                ((-1)*(this.Momentum))
                /
                (
                    this.P.length
                    *
                    Math.sin((
                        this.cable.angle.betweenP ?
                        this.cable.angle.magnitude
                        :
                        (
                            180-(
                                (180-this.P.angle) + this.cable.angle.magnitude
                            )
                        )
                    )*(Math.PI/180))
                );
        };
    };

    export const cat = new Cat;
    export { Problem };
</script>